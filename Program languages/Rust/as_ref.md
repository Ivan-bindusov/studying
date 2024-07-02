#as_ref #as_mut
impl<T> Option<T> {
    pub fn as_ref(&self) -> Option<&T>;
}

It demotes the `Option<T>` to an Option to a reference to its internals.
Mutable_ version of this method is `as_mut`

