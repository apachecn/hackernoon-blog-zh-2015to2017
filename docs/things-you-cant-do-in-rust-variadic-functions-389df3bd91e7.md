# Rust 中不能做的事情:可变函数

> 原文：<https://medium.com/hackernoon/things-you-cant-do-in-rust-variadic-functions-389df3bd91e7>

我爱 Rust，希望它更好。开发团队知道提出的所有问题。我只是想引发讨论和热情，让一门好的语言变得更好。

```
#![feature(c_variadic)]
#[no_mangle]
pub unsafe extern "C" fn func(fixed: u32, mut args: ...) {
   let x: u8 = args.arg();
   let y: u16 = args.arg();
   let z: u32 = args.arg();
   println!("{} {} {} {}", fixed, x, y, z);
}
```

结果(编译时错误):

```
**error[E0554]: #[feature] may not be used on the stable release channel
  |
1** **|** #![feature(c_variadic)]
  **| ^^^^^^^^^^^^^^^^^^^^^^^****error: aborting due to previous error(s)**
```

Rust 有可变的宏。Rust 可以使用 C 变量函数。RFC 2137 只涵盖了从 Rust 内部定义变量函数的情况。很难考虑垃圾收集和生命周期，所以这个特性可能会局限于基本类型或类似的折衷。也许这只是与宏重叠，该功能是不必要的。