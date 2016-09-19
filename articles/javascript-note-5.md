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
#### 部署方法

###### 部署在Object對象 - 構造函數方法

方法存在Object中，如果其它對象想要使用，必須呼叫Object的方法。

```
Object.print = function(o){console.log(o)}
var o = new Object();
Object.print(o);   // Object
```

###### 部署在Object.prototype - 實例方法

**所有構造函數都有一個prototype屬性，指向一原型對象。為原型定義的所有屬性和方法，將被所有實例共享。**

```
Object.prototype.print = function(){console.log(this)};
var o = new Object();
o.print()      // Object
```