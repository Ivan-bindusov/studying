#as_ref 
## AsRef As A Generic Way To Borrow From Any Type
`AsRef` is Rust’s way to allow a programmer to pass references of different types to functions that can then use the cheap conversion and borrowing `AsRef` provides, given that they implement the corresponding `AsRef<T>`. This additionally allows you to open your APIs for more use cases and types.
`AsRef<T>` is the way of conversion if you want to **borrow**, while `Into<T>` is the way if you want to take **ownership** of the value and are additionally okay with a little more overhead for the conversion.
The standard library itself makes excessive use of `AsRef` where applicable. Many functions in the standard library accept `AsRef<str>`, for example, which automatically makes these functions usable for both `Strings`, and `strs`, as well as any variant of them.
## Example
![[2024-04-02 00 31 07.jpg]]
