#clone #to_owned
The difference between .clone() and .to_owned() occurs when applying either of the two methods on slices such as string slice &str or arrays with undefined capacity like &[i8] or &[u32], in their borrowed state (&T):
- .clone() generates a duplicate of types such as &str or &[u8] with the same type in its borrowed state (&T). This means using .clone() on &str and &[u8] will generate &str and                           &[u8] respectively.
![[Pasted image 20240405214923.png]]

- `.to_owned()` generates a duplicate of types such as `&str` or `&[u8]` with types that have ownership. This means using `.to_owned()` on `&str` and `&[u8]` will generate a `String` and a `Vec<u8>` respectively.
![[Pasted image 20240405215323.png]]

## Understanding more about the `.clone()` method
As the name suggests, the `.clone()` method generates a duplicate of an object similar to [the `Copy` trait](https://doc.rust-lang.org/std/marker/trait.Copy.html). **The difference between `Copy` and `Clone` is that `Copy` duplicates bits stored in the stack, while `Clone` might involve copying heap data**, which could or not result in a more expensive operation.
### Accessible with the `Clone` trait
The `.clone()` method is available to all types deriving the [`Clone` trait](https://doc.rust-lang.org/std/clone/trait.Clone.html). By default, all the primitive types (`str`, `i8`,`u8`, `char`,`array`, `bool`, etc), have access to the `.clone()` method.
![[Pasted image 20240405221351.png]]

If you are defining a struct and want to have access the `.clone()` method, you must make sure:
1. Every field in the struct is clonable
2. To Add the `Clone` derive attribute
To make this clear, if you create a `MyObject` struct like the following:
![[Pasted image 20240405222010.png]]

and later in your code you decide to create a variable storing an `MyObject` data type,
```
let my_object = MyObject::new();
```
you won’t have access to the `.clone()` method. Hence, `my_object` variable won’t have `.clone()` method and it will throw the following error if you attempt to use it.
```
// this will throw the following error: // error[E0599]: no method named `clone` found //for struct `MyObject` in the current scope

let cloned_my_object = my_object.clone();
```
To solve this issue, adding the `Clone` derive in the _struct_ `MyObject`
![[Pasted image 20240405222645.png]]

will give access to the .clone() method:
![[Pasted image 20240405222805.png]]
### Generating a duplicate with the same type

The `.clone()` generates a duplicate of an object `T` with the same type `T`, meaning if
- a is `u8`, the duplicate will be a `u8`
- a is `String`, the duplicate will be a `String`
- a is `&[&str]`, the duplicate will be a `&[&str]`
- a is `MyObject`, the duplicate will be a `MyObject`
![[Pasted image 20240405223218.png]]

It is possible to duplicate values with known size at compile time in their borrowed state `&T` to generate a duplicate in the owned state `T` using the `.clone()` method.
![[Pasted image 20240405224604.png]]
### Generates a duplicate of `&T` as `T` for scalar types and tuples

Rust has four #primary_scalar types:
- Integers
- Floating-point numbers
- Booleans
- Characters
They represent a single value. This means all integers (`12`), floating-point numbers (`3.4` ), booleans ( `true`,`false` ), and characters (`'a'`, `'z'`) have the same value no matter how many times you use them. It is because of this, that it makes it possible to generate an owned duplicate from a borrowing state. In other words, going from `&T` to `T`.
![[Pasted image 20240405231549.png]]

### Generates a duplicate of `&[T]` as `[T]` for arrays with a defined length
The `.clone()` method can duplicate of array when the original array has ownership, meaning going from `[T]` to `[T]`:
![[Pasted image 20240405231704.png]]
However, `.clone()` can also go from `&[T]` to `[T]` as long as the array capacity is defined.
![[Pasted image 20240405231744.png]]
The array capacity will also be implicitly defined even when you don’t assign a type definition (`&[T]`). In the following example, the duplicate generated is `[T]` from `[T]`.
![[Pasted image 20240405231939.png]]
However, if you define the type is of variable is an array without explicitly defining the capacity, `.clone()` will still generate a duplicate. However, this new duplicate will be still in a borrowing state. Hence, it will go from `&[T]` to `&[T]`.
### The `.clone()` not go from `&[T]` to `[T]` on arrays without a specified capacity
If you define the type of variable is an array without explicitly defining its capacity, `.clone()` will still generate a duplicate. However, this new duplicate will be still in a borrowing state. Hence, it will go from `&[T]` to `&[T]`.
![[Pasted image 20240405232359.png]]

## Understanding more about the `.to_owned()` method
As the Rust documentation states, the `.to_owned()` method is a generalization of the `Clone` trait to borrowed data. This means, `.to_owned()` **generates a duplicate value.** Hence, if you apply `.to_owned()` on `T` , the duplicate will be `T` .
![[Pasted image 20240406005324.png]]
One key aspect of `.to_owned()` is that this method is capable of constructing owned data from any borrow of a given type. This means, if you apply `.to_owned()` on any `&T`, the duplicate will be `T`.
![[Pasted image 20240406140345.png]]
### The `.to_owned()` method generates a `String` from `&str`
One question that comes for many Rust developers is: _How come the `.clone()` method generates a similar data type when cloning a string slice reference (`&str`) and the `.to_owned()` method_ _generates a `String`?_

To answer this question, you must understand the concepts of [ownership and borrowing](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html). The string slice reference `&str` is in its borrowed state. That means, that whichever variable stores the string slice reference does not own the value.

In the previous section you learned the `.clone()` method can still convert from `&T` to `T` where `T` could be a [primitive type such as a boolean or a number](https://doc.rust-lang.org/rust-by-example/primitives.html) or a custom struct such as `MyObject`. What is the difference with a `str`?

The string slice is not a primitive type but a sequence of characters. In other words, an array of characters `[T]`. A characteristic of [the string slice is that doesn’t have ownership](https://doc.rust-lang.org/book/ch04-03-slices.html#the-slice-type). The `.clone()` method attempts to make it possible to go from borrowed to owned, but that doesn’t mean always happens. As you know, that’s the case of the string slice reference `&str` .

Hence, the `.clone()` method does generate a duplicate of a string slice reference without ownership. That’s where the `.to_owned()` method differs from `.clone()`.

[The `.to_owned()` “generalizes” the `Clone` trait to construct data from borrow of a give type](https://doc.rust-lang.org/stable/std/borrow/trait.ToOwned.html). This implies that the core functionality of `to_owned()` is to make sure the duplicate value will always have ownership, even if this means having to allocate the values in a data type different from the original.

By generating a `String` from a string slice reference `&str` , `to_owned()` meats the criteria of providing ownership. As you should remember, a `String` value can have ownership. If you look at the definition of the `String`, you will find it is a struct based on a vector.
![[Pasted image 20240406152925.png]]
### The `.to_owned()` method generates a `Vector` from a reference array with undefined capacity
There is another interesting case of using the `.to_owned()` method results in a duplicate of a different type. This happens when applying `.to_owned()` to reference arrays with undefined capacity, such as,

- `&[i8]`
- `&[i16]`
- `&[i32]`
- `&[i64]`
- `&[i128]`
- `&[u8]`
- `&[u16]`
- `&[u32]`
- `&[u64]`
- `&[u128]`

generates a vector

- `Vec<i8>`
- `Vec<i16>`
- `Vec<i32>`
- `Vec<i64>`
- `Vec<i128>`
- `Vec<u8>`
- `Vec<u16>`
- `Vec<u32>`
- `Vec<u64>`
- `Vec<u128>`

respectively.
## Conclusion

 The key difference to remember is that while `.clone()` attempts to go from borrowed to owned state, that is not always possible as `Clone` works only from `&T` to `T`.

On the other hand, `to_owned()` makes sure the duplicate value has ownership even if that means having to use a different data type such as a `String` or a `Vec<u8>` when generating a duplicate for a `&str` or a `&[u8]` .