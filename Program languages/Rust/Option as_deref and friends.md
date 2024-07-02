In Rust, it is not uncommon to have to convert something like `&Option<String>` into `Option<&str>`. For example, imagine I’m storing an optional text description in a type, like this:
```
pub struct Foo {
    description: Option<String>,
    /* ... */
}
```
If I wanted someone with a reference to a `Foo` to be able to access the value of the description, I could create a getter, which usually looks like this:
```
impl Foo {
    pub fn description(&self) -> Option<&str> {
        self.description.as_ref().map(|string| string.as_str())

        // Or:
        // self.description.as_ref().map(String::as_str)

        // Or even (because String implements Deref<Target = str>):
        // self.description.as_ref().map(|string| &*string)
    }
}
```
However, you can write this even more concisely, using [`Option::as_deref`](https://doc.rust-lang.org/stable/std/option/enum.Option.html#method.as_deref):
```
impl Foo {
    pub fn description(&self) -> Option<&str> {
        self.description.as_deref()
    }
}
```
So what’s happening here? You can see in the [source code](https://doc.rust-lang.org/1.53.0/src/core/option.rs.html#1181), it looks very similar to our own long-hand implementation, but more generic. This is what it looks like, at the time of writing (Rust 1.53.0):
```
impl<T: Deref> Option<T> {
    pub fn as_deref(&self) -> Option<&T::Target> {
        self.as_ref().map(|t| t.deref())
    }
}
```
When I call `some_option.as_deref()`, it takes a reference to the inner value, and dereferences it using the type’s `Deref` implementation. In our specific case, `String` implements `Deref<Target = str>`, so it goes from an `&Option<String>`, to an `Option<&String>`, to an `Option<&str>`. Just like our original code!

This works for any type that implements `Deref`. In this case, `String` implements `Deref<Target = str>`, which allows a `&String` to be converted into `&str`. In human terms, we would call `String` (a string buffer) the “owned” string type, and `str` (a string slice) as the “borrowed” string type. [1](https://www.agausmann.me/2021/07/12/option-as-deref.html#fn:1) This implementation is also provided for other “owned” and “borrowed” type pairs, like `Vec<T>` and `[T]`, `CString` and `CStr`, and even `Box<T>` and `T`. [2](https://www.agausmann.me/2021/07/12/option-as-deref.html#fn:2)

Another example where this may be useful is in implementations of the [`Error` trait](https://doc.rust-lang.org/stable/std/error/trait.Error.html). Suppose I have an error type that looks like this:
```
pub struct MyError {
    source: Option<Box<dyn Error + 'static>>,
    /* ... */
}
```
The `Error` trait has a `source()` method that allows you to specify a wrapped “source” error. In my case, if my error value has a source, I’m storing it as a trait object in a `Box`, but that has to be adapted to match the type required by the `source()` method. In the past, I’d write the method like this:
```
impl Error for MyError {
    fn source(&self) -> Option<&(dyn Error + 'static)> {
        self.source.as_ref().map(Box::as_ref)
    }
}
```
This can also be made simpler using `Option::as_deref()`, since `Box<T>` implements `Deref<Target = T>`:
```
impl Error for MyError {
    fn source(&self) -> Option<&(dyn Error + 'static)> {
        self.source.as_deref()
    }
}
```
# Friends of `Option::as_deref`
- `Option` has another method, [`as_deref_mut`](https://doc.rust-lang.org/stable/std/option/enum.Option.html#method.as_deref_mut), which does the same thing but for mutable references. Instead of `Deref`, it requires `T: DerefMut`, and converts a `&mut Option<T>` into `Option<&mut <T as Deref>::Target>`.
    
- `Result` has its own [`as_deref`](https://doc.rust-lang.org/stable/std/result/enum.Result.html#method.as_deref) and [`as_deref_mut`](https://doc.rust-lang.org/stable/std/result/enum.Result.html#method.as_deref_mut), which convert `&[mut] Result<T, E>` into `Result<&[mut] <T as Deref>::Target, &[mut] E>`.
