## map
#ref #ref_map
```
map<U, F>(orig: Ref<'b, T>, f: F) -> Ref<'b, U>
    where F: FnOnce(&T) -> &U,
          U: ?Sized

```
Make a new Ref for a component of the borrowed data.

This is an associated function that needs to be used as `Ref::map(...)`. A method would interfere with methods of the same name on the contents of a `RefCell` used through `Deref`.
```
use std::cell::{RefCell, Ref};

let c = RefCell::new((5, 'b'));
let b1: Ref<'_, (u32, char)> = c.borrow();
let b2: Ref<'_, u32> = Ref::map(b1, |t| &t.0);
assert_eq!(*b2, 5)
```
```
pub fn peek_front(&self) -> Option<Ref<T>> {
    self.head.as_ref().map(|node| {
        Ref::map(node.borrow(), |node| &node.elem)
    })
}

```
## map_split
#map_split
```
pub fn map_split<U, V, F>(orig: Ref<'b, T>, f: F) -> (Ref<'b, U>, Ref<'b, V>) where
    F: FnOnce(&T) -> (&U, &V),
    U: ?Sized,
    V: ?Sized,

```
Splits a `Ref` into multiple `Ref`s for different components of the borrowed data.

The `RefCell` is already immutably borrowed, so this cannot fail.

This is an associated function that needs to be used as `Ref::map_split(...)`. A method would interfere with methods of the same name on the contents of a `RefCell` used through `Deref`.
### examples
```
use std::cell::{Ref, RefCell};

let cell = RefCell::new([1, 2, 3, 4]);
let borrow = cell.borrow();
let (begin, end) = Ref::map_split(borrow, |slice| slice.split_at(2));
assert_eq!(*begin, [1, 2]);
assert_eq!(*end, [3, 4]);
```

```
impl<'a, T> Iterator for Iter<'a, T> {
    type Item = Ref<'a, T>;
    fn next(&mut self) -> Option<Self::Item> {
        self.0.take().map(|node_ref| {
            self.0 = node_ref.next.as_ref().map(|head| head.borrow());
            Ref::map(node_ref, |node| &node.elem)
        })
    }
}
```

```
cargo build

error[E0521]: borrowed data escapes outside of closure
   --> src/fourth.rs:155:13
    |
153 |     fn next(&mut self) -> Option<Self::Item> {
    |             --------- `self` is declared here, outside of the closure body
154 |         self.0.take().map(|node_ref| {
155 |             self.0 = node_ref.next.as_ref().map(|head| head.borrow());
    |             ^^^^^^   -------- borrow is only valid in the closure body
    |             |
    |             reference to `node_ref` escapes the closure body here

error[E0505]: cannot move out of `node_ref` because it is borrowed
   --> src/fourth.rs:156:22
    |
153 |     fn next(&mut self) -> Option<Self::Item> {
    |             --------- lifetime `'1` appears in the type of `self`
154 |         self.0.take().map(|node_ref| {
155 |             self.0 = node_ref.next.as_ref().map(|head| head.borrow());
    |             ------   -------- borrow of `node_ref` occurs here
    |             |
    |             assignment requires that `node_ref` is borrowed for `'1`
156 |             Ref::map(node_ref, |node| &node.elem)
    |                      ^^^^^^^^ move out of `node_ref` occurs here

```
