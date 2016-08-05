JavaScript發出異步請求（AJAX）過去普遍仰賴XMLHttpRequest，然而XMLHttpRequest不符合關注分離原則，輸入輸出和事件追蹤狀態混雜在同一對象裡。

而且基於事件的模型與最近JavaScript流行的Promise和基於生成器的異步編模型不太搭，事件模型在異步處理上有點過時了。

新的Fetch API打算修正這些缺陷。它引入一個實用的函數fecth()用來捕捉網路上檢索一個資源的意圖。


[fetch介紹](https://segmentfault.com/a/1190000003810652)

[This API is So Fetching 譯文](http://www.w3ctech.com/topic/854)

