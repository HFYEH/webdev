這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## 包裝對象

原始類型的值可以包裝成對象。

```
// 構造函數調用可以生成新的對象
var x = new Number(123);
var y = new String('aaa');
var z = new Boolean(true);

// 包裝後的對象有一[[primitiveValue]]屬性，外部無法直接調用，須要透過valueOf或toString才能取得

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

```
'abc'.length
// 3
```

`'abc'`應是字串，之所以可以調用`length`屬性，是因為JavaScript引擎將字串自動生成包裝對象，使用length後就立刻銷毀。包裝