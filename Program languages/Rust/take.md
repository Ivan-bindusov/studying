 #option_take
![[Pasted image 20240407135655.png]]

![[Pasted image 20240407134354.png]]
![[Pasted image 20240407134419.png]]
**## Fix by Wrapping With `Option<T>`

The problem here is because `Bar.bar` take ownership of `self` (rather than reference as `&self`). In this case, as we are holding the reference of `bar` (i.e. `foo.bar`) and because `Bar` doesn’t implement `Copy` trait, so we can not transfer the ownership out.

One fix is as below:
![[Pasted image 20240407134458.png]]
