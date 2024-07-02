#Arc #Rc 
The type `Arc<T>` represents the ownership of a type `T` value in a shared medium allocated in the heap. A new `Arc` instance is formed when a reference count increases, connecting to the same allocation on the heap as the originating `Arc`.

We need to know that `clone()` on an `Arc<T>` will not clone `T` but create another owned handle. It is just a pointer that behaves as if it holds the underlying object.
## [Difference Between `Arc<T>` and `Rc<T>` in Rust](https://www.delftstack.com/howto/rust/rust-arc-clone/#difference-between-arct-and-rct-in-rust)

Multiple threads can increment and decrease the counter in an atomic manner using `Arc` without issue. As a result, `Arc` isn’t restricted to thread boundaries.

However, `Arc` is not meant to provide simultaneous mutable access from multiple owners. So, `Rc<T>` is for multiple ownership, whereas `Arc<T>` is for multiple ownership beyond thread boundary.

`Rc<T>` allows us to have several owning pointers to the same data, and the data is freed after all pointers have been released.