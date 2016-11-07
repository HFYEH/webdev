本文是閱讀阮一峰的文章後個人思路整理。

JavaScript是單線程的，一次只能完成一件任務，如果有多項任務，就須排隊等候。

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






說個最近碰到的例子。使用者開啟網頁後，要先從localStorage取出一些id（聊天室），再根據這些id向伺服器要一些資料(function f1)，再進行websocket連線，再對每個聊天室取得最近的聊天訊息(function f2)。由於從伺服器取回資料使用了非同步機制，在等待伺服器回應的時間，就會先去執行f2，因此如果只是簡單的把f2放在f1後面，就有可能會出錯。


我又不想把f2寫在f1裡面，因為f1是向伺服器取得json用的，f2是聊天室的功能，兩者不應該藕合在一起。



JavaScript為single thread，因此異步編程是必須的。

異步編程大概有有[四種]。(http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html)




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

