#Mutex #Arc #thread
In order to provide that ownership across threads—similar to what Rc does within a single
thread—Rust provides the concept of an Arc, an **atomic reference counter**. Using this
Mutex on top, it's the thread-safe equivalent of an Rc wrapping a RefCell, a reference
counter that wraps a mutable container. To provide an example, this works nicely:
![[Pasted image 20240405011123.png]]
Arc::clone() - This creates another pointer to the same allocation, increasing the strong reference count.
[[Shared ownership (Arc)]]
