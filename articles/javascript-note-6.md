這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## 包裝對象

原始類型的值可以包裝成對象。

```
// 構造函數調用可以生成新的對象
var x = new Number(123);
var y = new String('aaa');
var z = new Boolean(true);

typeof x  // "object"
typeof y  // "object"
typeof z  // "object"

// 以下皆為false，因為類型不同，而且對象比較值是比較引用是否相同
x === 123   // false
y === 'aaa' // false
z === true  // false

// 函數調用則將內部值轉為原始數值
Number(123)   // 123
String('aaa') // "aaa"
Boolean(true) // true
```

## 包裝對象實例的方法

包裝對象可以使用Object提供的原生方法，主要為`valueOf`和`toString`。

`valueOf`用於返回對對象的原始類型

`toString`返回實例對應的字串形式

## 自動轉換
