# JavaScript 中的 Array.prototype.reduce()

其實就是Ruby的\#inject，如果知道\#inject就可以跳過了~~而Ruby本身也有\#reduce就是了。（在ruby中的\#reducer等於\#inject + block）

ES5中的Array.prototype.reduce\(\)

`arr.reduce( callback [, initialValue])`

參數是一個callback函數，reducer會由左至右為arr中的每個元素呼叫一次該callback函數，並將函數的回傳值當作下一次呼叫callback函數的參數傳入。直到最後一個代完後回傳值，原本的對象不會改變。

****callback傳入值為previouseValue, currentValue, index, array，一般傳入前兩個即可****


