- Send: A data type is safe to send (move) from one thread to the other  
- Sync: The data type can be shared across threads without manual locks or mutex  areas

These marker traits are implemented in all basic types of the standard library and can be  
inherited for custom types (if all properties of a type are Sync, then the type itself is Sync  
too).  
Implementing Sync or Send is unsafe because there is no way for the compiler to know if  
you are right and the code can be shared/sent between threads, which is why it's very  
unusual to do this.
https://doc.rust-lang.org/1.31.0/book/ch16-04-extensible-concurrency-sync-and-send.html
