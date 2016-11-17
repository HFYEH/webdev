### 前言：

JavaScript發出異步請求（AJAX）過去普遍仰賴XMLHttpRequest，然而XMLHttpRequest不符合關注分離原則，輸入輸出和事件追蹤狀態混雜在同一對象裡。

而且基於事件的模型與最近JavaScript流行的Promise和基於生成器的異步編模型不太搭，事件模型在異步處理上有點過時了。

新的Fetch API打算修正這些缺陷。它引入一個實用的函數fecth()用來捕捉網路上檢索一個資源的意圖。


### XMLHttpRequest
http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html




### 範例：

使用XHR發送json請求：

```
var xhr = new XMLHttpRequest();

xhr.open('GET', url);
xhr.responseType = 'json';
xhr.onload = function() { console.log(xhr.response); }; xhr.onerror = function() { console.log("Oops, error"); }; xhr.send();
```

使用fetch
```
fetch(url).then(function(response){
  return response.json();
}).then(function(data) {
  console.log(data);
}).catch(function(e) {
  console.log("Oops, error");
 });
```

使用ES6的箭頭函數
```
fetch(url)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(e => console.log("Oops, error", e))

```
使用async/await做最終優化
```
try {
  let response = await fetch(url);
  let data = response.json();
   console.log(data);
} catch(e) {
  console.log("Oops, error", e);
}
// 注：这段代码如果想运行，外面需要包一个 async function
```
作者預測async/await這類新異步語法出現，第三方的Promise會逐漸被標準Promise取代，使用polyfill是比較明智的做法。

一些重點是：
1. fetch預設不帶cookiie
2. 只有在發生網路錯誤導致請求不能完成才會被reject，404, 400, 500則否


### Promise

整理自[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

Fetch API是基於Promise標準庫來做異步請求的。一個Promise對象代表一個還未完成，但預期將來會完成的操作。

###### 函數型式與參數

`new Promise(function(resolve, reject){...});`

resolve: 處理執行成功的場景

reject: 處理執行失敗的場景

此對象是fetch的返回值，此返回值promise創建時未必已知。它允許會異步請求的成功或失敗指定處理方法。

###### Promise的狀態

1. pending: 初始狀態
2. fulfilled: 成功的操作(settled)
3. rejected: 失敗的操作(settled)

pending狀態可轉為fulfilled或rejected，當狀態發生轉喚，promise.then綁定的方法就會被調用。
![](https://mdn.mozillademos.org/files/8633/promises.png)

Promise.prototype.then和Promise.prototype.catch方法返回promises，所以可以被鏈式調用。


###### Fetch API
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch)


### 參考資料：

[fetch介紹](https://segmentfault.com/a/1190000003810652)

一些fetch API的例子說明
[This API is So Fetching 譯文](http://www.w3ctech.com/topic/854)

async/await的例子說明
[告別 JavaScript 的 Promise！迎接 Async/Await 的到來](http://jigsawye.com/2016/04/18/understanding-javascript-async-await/)

待讀
[JavaScript Promise API](https://segmentfault.com/a/1190000004476610)

[深入浅出Fetch API](http://wwsun.github.io/posts/fetch-api-intro.html)

[Fetch API](https://segmentfault.com/a/1190000006039533)

[传统 Ajax 已死，Fetch 永生](https://github.com/camsong/blog/issues/2)

[你真的会使用XMLHttpRequest吗？](https://segmentfault.com/a/1190000004322487)


阮一峰的《深入掌握 ECMAScript 6 异步编程》系列，超好懂！

[Generator 函数的含义与用法](http://www.ruanyifeng.com/blog/2015/04/generator.html)
