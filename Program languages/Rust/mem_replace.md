#mem_replace
```
pub fn replace<T>(dest: &mut T, src: T) -> T
```
Moves `src` into the referenced `dest`, returning the previous `dest` value.

Neither value is dropped.

- If you want to replace the values of two variables, see [`swap`](https://doc.rust-lang.org/std/mem/fn.swap.html "fn std::mem::swap").
- If you want to replace with a default value, see [`take`](https://doc.rust-lang.org/std/mem/fn.take.html "fn std::mem::take").

## Examples

A simple example:
```
use std::mem;

let mut v: Vec<i32> = vec![1, 2];

let old_v = mem::replace(&mut v, vec![3, 4, 5]);
assert_eq!(vec![1, 2], old_v);
assert_eq!(vec![3, 4, 5], v);
```
Note that `T` does not necessarily implement [`Clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html "trait std::clone::Clone"), so we can’t even clone `self.buf[i]` to avoid the move. But `replace` can be used to disassociate the original value at that index from `self`, allowing it to be returned:
```
use std::mem;

impl<T> Buffer<T> {
    fn replace_index(&mut self, i: usize, v: T) -> T {
        mem::replace(&mut self.buf[i], v)
    }
}

let mut buffer = Buffer { buf: vec![0, 1] };
assert_eq!(buffer.buf[0], 0);

assert_eq!(buffer.replace_index(0, 2), 0);
assert_eq!(buffer.buf[0], 2);
```
## From stack overflow
It's not always possible to write the direct code, due to Rust's ownership. If `self.next` is storing a non-`Copy` type (e.g. `Vec<T>` for any type `T`) then `let tmp = self.next;` is taking that value out of `self` by-value, that is, moving ownership, so the source should not be usable. But the source is behind a reference and references must always point to valid data, so the compiler cannot allow moving out of `&mut`: you get errors like ``cannot move out of dereference of `&mut`-pointer``.

`replace` gets around these issues via `unsafe` code, by internally making the guarantee that any invalidation is entirely made valid by the time `replace` returns.