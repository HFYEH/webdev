這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## 包裝對象

原始類型的值可以包裝成對象。

```javascript
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

```javascript
'abc'.length
// 3
```

`'abc'`應是字串，之所以可以調用`length`屬性，是因為JavaScript引擎將字串自動生成包裝對象，使用length後就立刻銷毀。


## Boolean對象

```javascript
var bool = new Boolean(false)
typeof bool             // "object"
Boolean(bool)           // true, 因為是object
Boolean(bool.valueOf()) // false
Boolean(1)              // true
Boolean('false')        // true 
Boolean([])             // true 
Boolean({})             // true 
Boolean(function () {}) // true 
Boolean(/foo/)          // true
```

***也可以直接用`!!`取值***

## Number對象

以下是一些實例方法

```javascript
// toString參數是進位制
2.toString(2)   // "10"
2.toString(10)  // "2"

// toFixd參數是顯示的小數位數，四捨五入
(10.005).toFixed(2)  // 10.01

// toExponential
(10).toExponential   // "1e+1"
```

## String對象

以下是一些實例方法

```javascript
'abc'.length           // 3

// 找出某位置上的字元
'abc'.charAt(1)        // "b"

// 找出某位置上字元的Unicode，String.fromCharCode()的逆操作
'abc'.charCodeAt(1)   // 98

// 連接兩字串後返回，不改變原字串
'abc'.concat('def')   // "abcdef"

// slice功能與array相同，參數為開始和結束位置
'abc'.slice(1)        // "bc"

// substr參數為開始位置和擷取長度，不改變原字串
'abc'.substr(1,1)     // "b"

// indexOf, lastIndexOf 參數為欲搜尋的字元，和從起始搜尋位置
'hello world'.indexOf('o', 6)  // 7

// trim 去兩端空格和換行符號等

// toLowerCase, toUpperCase

// match, replace 多用在正則表達式

// split是join的相反，將string切成array
```