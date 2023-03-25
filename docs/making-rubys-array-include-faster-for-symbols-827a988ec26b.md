# 制作 Ruby 的 Array.include？符号速度更快

> 原文：<https://medium.com/hackernoon/making-rubys-array-include-faster-for-symbols-827a988ec26b>

这一切都开始做一些重构——我在玩`Array`和`Set`,一些东西诱使我去做一个简单的基准测试，结果显示出奇怪的结果。这是我描述如何修复它的第二部分。这是关于构建 Ruby 的代码，用`lldb`调试它，让它更快！

这一切都始于[标杆](https://github.com/pawelniewie/benchmark-set-array-contains/blob/master/ruby/array_include.rb) `[Array.include?()](https://github.com/pawelniewie/benchmark-set-array-contains/blob/master/ruby/array_include.rb)`

```
Comparison:
               array:  1332567.6 i/s
       array symbols:  1154135.9 i/s - 1.15x  slower
```

符号数组比字符串数组慢，这难道不奇怪吗？符号应该是很快的！

我下载了 [Ruby 的源代码](https://github.com/ruby/ruby)开始看。

我最初认为问题出在 Symbol 的`==`上，所以我去寻找它。我在`string.c`很容易地找到了一个字符串

```
rb_define_method(rb_cString, "==", rb_str_equal, 1);
```

但是`symbol.c`没有遵循模式，因为它也在`string.c` :-)

```
rb_define_method(rb_cSymbol, "==", sym_equal, 1);
```

确定数组在哪里也很简单，这很容易:

```
rb_define_method(rb_cArray, "include?", rb_ary_includes, 1);
```

```
VALUE
rb_ary_includes(VALUE ary, VALUE item)
{
    long i;
    VALUE e;

    for (i=0; i<RARRAY_LEN(ary); i++) {
    e = RARRAY_AREF(ary, i);
    switch (rb_equal_opt(e, item)) {
      case Qundef:
        if (rb_equal(e, item)) return Qtrue;
        break;
      case Qtrue:
        return Qtrue;
    }
    }
    return Qfalse;
}
```

这让我想到了`vm_insnhelper.c` `rb_equal_opt`然后又想到了`opt_eq_func`，感觉有些不对劲:

```
static
#ifndef NO_BIG_INLINE
inline
#endif
VALUE
opt_eq_func(VALUE recv, VALUE obj, CALL_INFO ci, CALL_CACHE cc)
{
#define BUILTIN_CLASS_P(x, k) (!SPECIAL_CONST_P(x) && RBASIC_CLASS(x) == k)
#define EQ_UNREDEFINED_P(t) BASIC_OP_UNREDEFINED_P(BOP_EQ, t##_REDEFINED_OP_FLAG)
    if (FIXNUM_2_P(recv, obj)) {
    if (EQ_UNREDEFINED_P(INTEGER)) {
        return (recv == obj) ? Qtrue : Qfalse;
    }
    }
    else if (FLONUM_2_P(recv, obj)) {
    if (EQ_UNREDEFINED_P(FLOAT)) {
        return (recv == obj) ? Qtrue : Qfalse;
    }
    }
    else if (BUILTIN_CLASS_P(recv, rb_cFloat)) {
    if (EQ_UNREDEFINED_P(FLOAT)) {
        return rb_float_equal(recv, obj);
    }
    }
    else if (BUILTIN_CLASS_P(recv, rb_cString)) {
    if (EQ_UNREDEFINED_P(STRING)) {
        return rb_str_equal(recv, obj);
    }
    }
#undef EQ_UNREDEFINED_P
#undef BUILTIN_CLASS_P

    {
    vm_search_method(ci, cc, recv);

    if (check_cfunc(cc->me, rb_obj_equal)) {
        return recv == obj ? Qtrue : Qfalse;
    }
    }

    return Qundef;
}
```

我本想说我讨厌那样的格式，但结果是因为他们在文件中混合了空格和制表符。咄！

看看代码，很明显——符号没有特例。我想我有点期待 Ruby 已经优化了用 c 编写的方法。现在是时候弄清楚如何编写缺失的方法了。

首先，一些设置。运行一次以准备制作文件(Mac OS X):

```
autoconf 
CFLAGS="-O0 -ggdb" ./configure --prefix=/usr/local/opt/rbenv/versions/2.5.0 --with-openssl-dir="$(brew --prefix openssl)" --disable-install-doc
```

然后进行编译:

```
make && make check && make install
```

Ruby C API 指南是理解代码中发生的事情的一个很好的起点。最重要的教训——你不能`printf` a `VALUE`，有`rb_p`为那。

你也可以用`lldb`调试东西，这很简单(虽然不是`gdb`，所以[命令与](https://lldb.llvm.org/tutorial.html)不同):

`lldb /usr/local/opt/rbenv/versions/2.5.0/bin/ruby -- -e "if [:test, :another].include?(:test); puts 'A'; end"`

```
breakpoint set --file vm_insnhelper.c --line 1279 
process launch 
process continue
```

当然，我是如此渴望，以至于在最终搜索到 Ruby C API 指南之前，我尝试了许多东西:-D 我认为它很容易理解，并且我可以处理 C 代码，因为它回到了过去。

当我阅读指南时，很明显我必须使用`SYMBOL_P`:

```
diff --git a/vm_insnhelper.c b/vm_insnhelper.c
index a991e59..476b5e2 100644
--- a/vm_insnhelper.c
+++ b/vm_insnhelper.c
@@ -1296,6 +1296,11 @@ opt_eq_func(VALUE recv, VALUE obj, CALL_INFO ci, CALL_CACHE cc)
        return rb_str_equal(recv, obj);
    }
     }
+    else if (SYMBOL_P(recv) && SYMBOL_P(obj)) {
+        if (EQ_UNREDEFINED_P(SYMBOL)) {
+       return rb_obj_equal(recv, obj);
+   }
+    }
 #undef EQ_UNREDEFINED_P
 #undef BUILTIN_CLASS_P
```

现在再次运行基准测试:

```
Comparison:
       array symbols:  1630417.3 i/s
               array:  1372269.7 i/s - 1.19x  slower
```

符号现在更快了！从一开始就应该这样。

如你所见，浏览 C 代码很容易。此外，如果您在基准测试中发现一些问题，这可能只是代码中的一个简单错误。你需要记住 Ruby 有两层——Ruby 代码和 C 代码。如果你的 Ruby 代码看起来不错，那可能是有问题的 C 代码。

创建了[公关并修复了](https://github.com/ruby/ruby/pull/1540)。

> 像这样？请点击下面的♥，以便其他人可以找到它！

*原载于 2017 年 3 月 21 日*[*pawelniewiadomski.com*](https://pawelniewiadomski.com/2017/03/21/making-ruby-array-include-faster/)*。*