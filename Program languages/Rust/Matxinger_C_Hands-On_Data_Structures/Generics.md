#generic
```
fn my_generic_func<T: MyTrait>(t: T) {  
// code  
}  
// ... is the same as  
fn my_generic_func <T>(t: T) where T: MyTrait {  
// code  
}  
// but better use in 2018 and beyond  
fn my_generic_func(t: impl MyTrait) {  
// code  
}
```
Additionally, the 2018 impl Trait syntax simplifies single-trait requirements (to do static
instead of dynamic dispatch) for input and return parameters, thereby eliminating the need
for a Box or lengthy type constraints (such as MyTrait in the preceding snippet). Unless
multiple trait implementations are required (for example, fn f(x: T) where T: Clone
 Debug + MyTrait {}), the impl Trait syntax allows us to put them where they
matter, which is into the parameter list:
```
fn my_generic_func<T>(t: T) {  
// code  
}  
// ... is the same as  
fn my_generic_func <T: Sized>(t: T) {  
// code  
}
```
When working with generics, the situation is a bit more complex. Type parameters are  
Sized by default (see the preceding snippet), which means that they will not match  
unsized types. To match those as well, the special ?Sized type constraint can be used. This  
snippet also shows the required change to passing in a reference:
```
fn my_generic_func <T: ?Sized>(t: &T) {  
// code  
}
```
