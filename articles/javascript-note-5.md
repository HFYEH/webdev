這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## Object

所有的JavaScript對象都繼承自Object對象。Object對象本身也是一個構造函數，可以透過它生成對象。

```
var o = new Object()  // 全等於 o = {}

// 如果傳入的參數是對象，就返回該對象
var o1 = {a: 1};
var o2 = new Object(o1);
o1 === o2 // true
// 如果傳入的參數是原始類型，就返回其值對應的包裝對象
new Object(123) instanceof Number // true
```

