### 前言

本文是閱讀阮一峰老師的非同步處理的相關文章後個人思路整理。

### 同步與非同步 

JavaScript是單線程的，一次只能完成一件任務，如果有多項任務，就須排隊等候，前一個任務完成之後，才能執行下一個任務，這是同步模式。

舉個例子

```
f1 = function(){console.log("f1")}
f2 = function(){console.log("f2")}

f1()
f2()
// "f1"
// "f2"
```
兩個函數本身都是同步函數，因此f1執行完才會執行f2。

假如f1須要較長的執行時間，而且含有等待IO的時間，則這些等待的時間是種浪費，有沒有方法讓我們在等待IO的時間裡先繞過去做f2呢？我們須要的是非同步模式。

非同步編程大概有下面[四種](http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)：

1. 是把任務使用setTimeout(fn, 0)排進下一個任務之中。setTimeout()是塞任務用的，跟一般的函數呼叫後馬上執行是有差異的。不過作者給的例子不太正確，實際跑起來還是同步的。
2. 監聽事件，並綁定回調函數(callback)。這個就很常見了，會用jQuery的應該不陌生。
3. publish/subscribe，沒有使用過，看起來跟websocket的模式有點像，其實也是一種回調。相比於上者，有統一管理的好處。
4. Promise對象。CommonJS工作處提出的歸範，旨在為非同步操作提供統一的接口。

如標題所說，以下將只專注於Promise。

### Promise

Promise是一個對象，目的在當作非同步操作和回調函數的中介，使得非同步操作和同步操作具有同樣的接口，不用再用嵌套的方式撰寫非同步操作。

##### 接口

Promise對象有三種狀態，`pending`, `resolved`, `rejected`。

變化途徑只有兩種`pending -> resolved`, `pending -> rejected`。

變化只會發生一次，一但狀態改變，就不會再有新的狀態變化。因此Promise對象最後只有兩種結果：

1. 成功，Promise對象傳回一個值，狀態變為resolved。
2. 或失敗，Promise對象拋出例外，狀態變為rejected。

使用`then`添加回調函數，它接受兩個函數作為參數，第一個是成功時的回調函數，第二是失敗時的回調函數。一但Promise對象狀態改變，就會調用相應的函數。

假設promise是一個Promise對象

```
promise
.then(step1)
.then(step2)
.then(console.log("Success"), console.log("Error"))
```

以上promise如果狀態改變為resolved，就會沿著`step1`, `step2` `console.log("Success")`一路執行下來，如果失敗，就會往下找到第一個操作失敗時的回調，也就是`console.log("Error")`。這也表明，失敗的回調是可以省略的，而且錯誤有傳遞性。

因為Promise成功時會回傳值，從同步的角度，上面的程式大約等於

```
try{
  var v1 = step1(promise)
  var v2 = step2(v1)
  console.log("Success")
} catch (error)
  console.log("Error")
}
```


### 


說個最近碰到的例子。使用者開啟網頁後，要先從localStorage取出一些id（聊天室），再根據這些id向伺服器要一些資料(function f1)，再進行websocket連線，再對每個聊天室取得最近的聊天訊息(function f2)。由於從伺服器取回資料使用了非同步機制，在等待伺服器回應的時間，就會先去執行f2，因此如果只是簡單的把f2放在f1後面，就有可能會出錯。


我又不想把f2寫在f1裡面，因為f1是向伺服器取得json用的，f2是聊天室的功能，兩者不應該藕合在一起。









JavaScript為single thread，因此異步編程是必須的。




### 參考資料：

[ECMAScript 6 入門](http://es6.ruanyifeng.com/) -  阮一峰

[Promise對象](http://javascript.ruanyifeng.com/advanced/promise.html) - 阮一峰

《深入掌握 ECMAScript 6 异步编程》系列文章四篇 - 阮一峰

[Generator函数的含义与用法](http://www.ruanyifeng.com/blog/2015/04/generator.html)

[Thunk函数的含义与用法](http://www.ruanyifeng.com/blog/2015/05/thunk.html)

[co函数库的含义与用法](http://www.ruanyifeng.com/blog/2015/05/co.html)

[async函数的含义与用法](http://www.ruanyifeng.com/blog/2015/05/async.html)


###### setTimeout 和 setTimeInterval

[JavaScript异步机制](http://www.cnblogs.com/zhaodongyu/p/3922961.html)

