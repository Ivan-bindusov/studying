# into_boxed_slice()

```
pub fn into_boxed_slice(self) -> Box<[T], A>
```
Converts the vector into [`Box<[T]>`](https://doc.rust-lang.org/std/boxed/struct.Box.html "struct std::boxed::Box").
This conversion does not allocate on the heap and happens in place.
Before doing the conversion, this method discards excess capacity like [`shrink_to_fit`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.shrink_to_fit "method std::vec::Vec::shrink_to_fit").
```
let v = vec![1, 2, 3];

let slice = v.into_boxed_slice();
```
```
let mut vec = Vec::with_capacity(10);
vec.extend([1, 2, 3]);

assert!(vec.capacity() >= 10);
let slice = vec.into_boxed_slice();
assert_eq!(slice.into_vec().capacity(), 3);
```
**Input and output.** Suppose we want to create a Vector of structs. The struct name is BirdInfo, and it contains information about birds. We can use Box for the vector.
![[Pasted image 20240422144505.png]]

![[Pasted image 20240422145853.png]]
