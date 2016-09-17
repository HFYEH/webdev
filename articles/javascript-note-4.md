這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## Error對象

> JavaScript解析或执行时，一旦发生错误，引擎就会抛出一个错误对象。JavaScript原生提供一个Error构造函数，所有抛出的错误都是这个构造函数的实例。 

```
var err = new Error('出错了');err.message // "出错了"err.name    // "Error"err.stack   // "Error: 出错了 at <anonymous>:1:11"
// message是必要的屬性，後二者是大多數JavsScipt引擎都會實現的屬性。
```

當錯誤被拋出時，程式中斷，不繼續向下執行。

## 原生錯誤類型

連上面介紹的，共有七種。

1. SyntaxError是解析代码时发生的语法错误。 
2. ReferenceError是引用一个不存在的变量时发生的错误。
3. RangeError是当一个值超出有效范围时发生的错误。主要有几种情况，一是数组长度为负数，二是Number对象的方法参数超出范围，以及函数堆栈超过最大值。
4. TypeError是变量或参数不是预期类型时发生的错误。比如，对字符串、布尔值、数值等原始类型的值使用new命令，就会抛出这种错误，因为new命令的参数应该是一个构造函数。 
5. URIError是URI相关函数的参数不正确时抛出的错误，主要涉及encodeURI()、decodeURI()、encodeURIComponent()、decodeURIComponent()、escape()和unescape()这六个函数。 
6. eval函数没有被正确执行时，会抛出EvalError错误。该错误类型已经不再在ES5中出现了，只是为了保证与以前代码兼容，才继续保留。 

http://javascript.ruanyifeng.com/grammar/error.html#toc1


## throw

> throw语句的作用是中断程序执行，抛出一个意外或错误。它接受一个表达式作为参数，可以抛出各种值。JavaScript引擎一旦遇到throw语句，就会停止执行后面的语句，并将throw语句的参数值，返回给用户。 

```
// 抛出一个字符串
throw "Error！";
// 抛出一个数值
throw 42;
// 抛出一个布尔值
throw true;
// 抛出一个对象
throw {toString: function() { return "Error!"; } };
```

## try...catch

為了處理Error，須使用此結構。
```
try {
  throw new Error('出错了!');
}
catch (e) {
  console.log(e.name + ": " + e.message);
  console.log(e.stack);
}
// Error: 出错了!
//   at <anonymous>:3:9
//   ...
```

在try中的如果有任何對象（包函Error）被throw語句拋出後，會直接進到catch中進行後續處理。

## try...catch...finally

不管是否發生拋出，都必須在最後執行的語句，放置在finally區塊中。

如果結構中有返回值，返回機制比較複雜一點，還是看[原教材](http://javascript.ruanyifeng.com/grammar/error.html#toc5)。
