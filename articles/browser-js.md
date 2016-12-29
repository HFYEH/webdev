這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

# 概述

# window對象

# history對象

history保存目前分頁的瀏覽記錄，是一array-like對象，其length屬性表示訪問過的網址。

`history.back()` 等同上一頁

`history.forward()` 等同下一頁

`history.go(1)` 等同下一頁

`history.go(0)` 等同重新整理

`history.pushState()` 對history增添一筆新記錄。

接收三個參數，

1. `state`如果觸發`popState`，會傳入callback function
2. `title`會被忽略
3. `url`網址

***注意，只能設定同源網址。而且只會造成網址列改變並且新增記錄，並不會造成頁面刷新。***

`history.replaceState()` 同上，只不過不是新增一筆記錄，而是取代當前的記錄

`history.state()` 返回當前的`state`對象

`history.popState()` 當history對象發生改變，會觸發此事件。但是如果是使用`pushState`或`replaceState`是不會觸發該事件的。如果切換到另一個文檔，此事件也不會觸發。

可以用下列方式新增callback
```
window.onpopstate = function(event){console.log(event.state}  // 即上述的state
```


