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

1. `state`如果觸發popState，會傳入callback function
2. `title`會被忽略
3. `url`網址

***注意，只能設定同源網址。而且只會造成網址列改變並且新增記錄，並不會造成頁面刷新。***

`history.replaceState()` 同上
