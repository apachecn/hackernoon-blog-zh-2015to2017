# Rust 中不能做的事情:复制闭包

> 原文：<https://medium.com/hackernoon/things-you-cant-do-in-rust-copy-closures-79d2c0586da>

我爱 Rust，希望它更好。开发团队知道提出的所有问题。我只是想引发讨论和热情，让一门好的语言变得更好。

```
fn main() {
   // Many closures can now be passed by-value to multiple functions:
   fn call<F: FnOnce()>(f: F) { f() }
   let hello = || println!("Hello, world!");
   call(hello);
   call(hello); // Many `Iterator` combinators are now `Copy`/`Clone`:
   let x = (1..100).map(|x| x * 5);
   let y = x.map(|x| x - 3); // moves `x` by `Copy`ing
   let _ = x.chain(y); // moves `x` again
   let _ = x.cycle(); // `.cycle()` is only possible when `Self: Clone` // Closures which reference data mutably are not `Copy`/`Clone`:
   let mut x = 0;
   let incr_x = || x += 1;
   call(incr_x);
   call(incr_x); // ERROR: `incr_x` moved in the call above. // `move` closures implement `Clone`/`Copy` if the values they capture
   // implement `Clone`/`Copy`:
   let mut x = 0;
   let print_incr = move || { println!("{}", x); x += 1; }; fn call_three_times<F: FnMut()>(mut f: F) {
      for i in 0..3 {
         f();
      }
   }
   call_three_times(print_incr); // prints "0", "1", "2"
   call_three_times(print_incr); // prints "0", "1", "2"
}
```

结果(编译时错误):

```
**error[E0277]: the trait bound `[closure@a.ru:9:24: 9:33]: std::clone::Clone` is not satisfied
   |
12** **| **  let _ = x.cycle(); // `.cycle()` is only possible when `Self: Clone`
   **| **            **^^^^^** **the trait `std::clone::Clone` is not implemented for `[closure@a.ru:9:24: 9:33]`
   |
   = note**: required because of the requirements on the impl of `std::clone::Clone` for `std::iter::Map<std::ops::Range<{integer}>, [closure@a.ru:9:24: 9:33]>`**error: aborting due to previous error(s)**
```

公平地说，这个特性已经部分实现了。要实现 Copy，编译器需要注意生存期和可变性，所以这不是一个简单的添加特性。到目前为止，已经介绍了许多常见的情况，但是正如 RFC (2132)示例所示，还有很长的路要走。目前，这是闭包的又一个缺陷。