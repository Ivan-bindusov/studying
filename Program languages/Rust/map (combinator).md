`Option` имеет встроенный метод, зовущийся `map()`, комбинатор для простого преобразования `Some -> Some` и `None -> None`. Для большей гибкости, несколько вызовов `map()` могут быть связаны друг с другом в цепочку.

==`match option { None => None, Some(x) => Some(y) }`== is such an incredibly common idiom that it was called `map`. `map` takes a function to execute on the `x` in the `Some(x)` to produce the `y` in `Some(y)`.

`map()` _consumes_ the contained value, which means the value does not live past the scope of the `map()` call

