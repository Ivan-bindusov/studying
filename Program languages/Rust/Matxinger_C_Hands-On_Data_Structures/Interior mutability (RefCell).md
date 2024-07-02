
#RefCell #ref_cell
RefCell maintains single  [[ownership]] of a value but allows mutable borrowing checked at runtime. Instead of  compiler errors, violating the rules of borrowing will lead to a runtime panic!, crashing  
the program.
![[2024-04-04 18 32 24.jpg]]
By using the RefCell function's #borrow_mut(), it will check for and enforce borrowing  
rules and panic in the case of a violation. Later on, we will also talk about the Mutex type,  
which is essentially a multithreaded version of these cells.

# Methods
- [[into_inner]]
- 
