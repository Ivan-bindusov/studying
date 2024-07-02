#move #spawn
![[Pasted image 20240404233209.png]]
[[Channels|However, for passing multiple messages into a thread or implementing an actor model]]

Capture a [closure](https://doc.rust-lang.org/book/ch13-01-closures.html)’s environment by value.

`move` converts any variables captured by reference or mutable reference to variables captured by value.
```
let data = vec![1, 2, 3];
let closure = move || println!("captured {data:?} by value");

// data is no longer available, it is owned by the closure
```
Note: `move` closures may still implement [`Fn`](https://doc.rust-lang.org/std/ops/trait.Fn.html "trait std::ops::Fn") or [`FnMut`](https://doc.rust-lang.org/std/ops/trait.FnMut.html "trait std::ops::FnMut"), even though they capture variables by `move`. This is because the traits implemented by a closure type are determined by _what_ the closure does with captured values, not _how_ it captures them:
```
fn create_fn() -> impl Fn() {
    let text = "Fn".to_owned();
    move || println!("This is a: {text}")
}

let fn_plain = create_fn();
fn_plain();
```
[[The Difference Between .clone() and .to_owned()]]

`move` is often used when [threads](https://doc.rust-lang.org/book/ch16-01-threads.html#using-move-closures-with-threads) are involved.
```
let data = vec![1, 2, 3];

std::thread::spawn(move || {
    println!("captured {data:?} by value")
}).join().unwrap();

// data was moved to the spawned thread, so we cannot use it here
```
