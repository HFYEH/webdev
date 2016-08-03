---
layout: post
title: 'Railsfun 2.5rd class (購物車)'
date: 2015-09-22 10:54
comments: true
categories: [railsfun]
---




基本 gem 的介紹與使用（有序）：
will_paginate
*simple_captcha
paperclip
devise
cancancan

實戰製作，需求：
要有分頁，註冊，圖片上傳
user & manager 權限分離
雙層（有回文結構）
手工 layout
mail 修改
流程問題

<=== Day2 ( 看情況此處可延長到Day3，另外做總複習，並實作完整的 demo 展示 )

所謂的攻擊
單欄位大量資料（在文字欄位貼上圖片的 raw data ）
巨量 bot 攻擊（寫隻 Ruby 機器人，打自己的作品 [ 先移除 form authenticity_token ] ）
流程中斷攻擊
防止手段

bootstrap & 外購的 template，如何包到專案裡面？

其餘的重要的東西 (( 這邊可留到 Rails-02 再講
`cache（ page / memcached [AR / objs]  ）
session server（ memcached ）
Redis

所謂的 Deploy，手法，種類（說明幾種方式）只實作
最簡單的 passenger + nginx（...因為要裝東西，所以預計應該會很久...）



以下垃圾話

測試後驗證day1可以跑到day1線 ok，不過有點緊...(JokerCatz)

day1應該不要自己手打所有測試，而是要有demo code，不然手打會拖太久...(JokerCatz)

...結論 day1 可用手打測試，不過用 demo : User / BookOwner / Book / Animal 來做
全部 relation 的範例即可...(JokerCatz)
