# Stack, heap and application binary segments

The `text` segment actually contains the compiled machine code, and the `data` segment contains application data found by the compiler while compiling.
```
                                     SEGMENTS
      low mem =============================================================
              |                       .text                               |
              |                contains machine code                      |
              =============================================================
              |                        .data                              |
              | contains data known to the app binary at compile time     |
              =============================================================
 addr[0x00F0] |                         HEAP                              |
              | contains dynamically allocated data created at run time   |
              | * data that lives "much" longer than one function         |
              | * data that is too big for the stack                      |
              | * data that must live behind a pointer, i.e. unknown size |
              |////////////////////////////////////////////////////////////
              |                         STACK                             |
              | contains memory allocated by functions at run time        |
              | * data with a known size                                  |
 addr[0xFFFF] | * data that mostly does not outlive the function itself   |
     high mem =============================================================
```

# Three types of string storage

A string in Rust can be stored in one of three ways:

- On the heap
- On the stack
- In the `data` segment of the application binary

 `String` is the heap allocated, growable, string type. The standard library `String` is always heap allocated.
 
 A `str` is a [Dynamically Sized Type](https://doc.rust-lang.org/nomicon/exotic-sizes.html). Furthermore, it is a primitive type, and unlike `String` you can't find it in the standard library since it's a compiler internal type.
 1. Anything that takes a `&str` can take a reference to a `String` and it will just work
2. We can get a `&str` sub-slice of a `String` by doing `&my_string[1..3]` which for the `String` `"abcd"` would be `"bcd"`.
Neither (1) nor (2) above need to allocate any extra memory except for the size of the pointer and the length.

## Data segment
All string literals, e.g. `let x = "hello"`, will have the type `&'static str`, and they are stored in the `data` segment of the application binary.
```
              PROGRAM                                     SEGMENTS
                                            ======================================= low mem
fn main() {                                 |              .text                  |
    // A str "string" with the value "abc"  =======================================
    // which is stored in the data segment  |              .data                  |
    let x: &'static str = "abc"; ----+      | addr[0x0008] <----------+           |
        |                            |      | type: *const u8         |           |
        |                            +------> value: "abc"            |           |
        |                                   ==========================|============
        |                                   |               HEAP      |           |
        |                                   |                         |           | addr[0x00F0]
        |                                   |                         |           |
        |                                   |/////////////////////////|///////////|
        |                                   |                         |           |
        |                                   |                         |           |
        |                                   |                         |           |
        |                                   | addr[0xFFF0]            |           |
        | // x is stored on the stack       | type: &'static str      |           | addr[0xFFFF]
        +-----------------------------------> value: 0x0008 + len: 3 -+           |
         // and contains a ptr and a len    |               STACK                 |
}                                           ======================================= high mem
```
## Stack

It's possible to store string data on the stack, one way would be to create an array of `u8` and then get a `&str` slice pointing into that array.

This is stolen from the [str documentation](https://doc.rust-lang.org/std/str/fn.from_utf8.html):
![[Pasted image 20240412155846.png]]


# A helpful tip

The reference variety of `String`: `&String`, should be avoided in favor of `&str`, unless there is a need for a "String out parameter". A "String out parameter", or `&mut String`, can be used when a currently owned String needs to be updated by a receiving function, without having to move it into, and then out of, that function.

In short:

- Use `String` for strings you need to change, or where a `String` is a required parameter.
- Use `&str` as function parameters when you need to read string data, since all types of strings can be cheaply turned into `&str`.
- Use `&mut String` only when you need a "String out parameter".
# Final words

We learned that

- `String` is for growable strings on the heap.
- `[u8; N]` (byte array) is for strings on the stack.
- string literals `"abc"` are strings in the `data` segment.
- `&str` is for peeking into string slices allocated on the heap, in the `data` segment, or on the stack.
- How `&str` points into the different types of string storage.
