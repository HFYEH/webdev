---
layout: post
title: 'websocket資料'
date: 2016-06-29 02:52
comments: true
categories: 
---
Websocket server的實作極多，本篇目標是想以 faye-websocket 這個 gem 做一個純websocket service。玩熟之後可能還會玩玩看 em-websocket 和 plezi。那麽，開始吧！

從Wikipedia的定義
> WebSocket是HTML5開始提供的一種在單個 TCP 連線上進行全雙工通訊的協定。WebSocket通訊協定於2011年被IETF定為標準RFC 6455，WebSocketAPI被W3C定為標準。
>
> 在WebSocket API中，瀏覽器和伺服器只需要做一個交握的動作，然後，瀏覽器和伺服器之間就形成了一條快速通道。兩者之間就直接可以資料互相傳送。

不過有另外兩個資料寫得比較好
[知乎原理介紹](https://www.zhihu.com/question/20215561)
[Railsfun JC 大解釋](http://railsfun.tw/t/rails-websocket/498/3)





## 參考資料

[Ruby & Websocket & websocket-js 支援與詳解 by JC](http://jokercatz.blogspot.tw/2013/04/ruby-websocket.html)

[菜鳥教程](http://www.runoob.com/html/html5-websocket.html)
[websocket之入门(一)](https://www.rails365.net/articles/websocket-zhi-ru-men-yi)
[Demo code - EM-websocket](https://blog.engineyard.com/2013/getting-started-with-ruby-and-websockets)
[使用Ruby打造一個簡單的Websocket server](http://blog.liveneeq.com/tech/2015/12/16/simple-websocket-server-in-ruby.html)
[Demo code by Heroku](https://devcenter.heroku.com/articles/ruby-websockets)

[幕凡投影片](http://www.slideshare.net/ryudoawaru/rt28-29828529)


Ruby Gem
https://faye.jcoglan.com/ruby.html
https://github.com/faye/faye-websocket-ruby
https://github.com/igrigorik/em-websocket
http://www.plezi.io/

[Ruby SSE Server 動手做](http://tonytonyjan.net/2015/11/05/concurrent-ruby/)
[Ruby EventMachine Web Socket Redis Pub/Sub Chat Room](http://ericlondon.com/2014/03/04/ruby-eventmachine-web-socket-redis-pub-sub-chat-room.html)
[WebSockets From Scratch](https://blog.pusher.com/websockets-from-scratch/)
[Building a simple websockets server from scratch in Ruby](http://blog.honeybadger.io/building-a-simple-websockets-server-from-scratch-in-ruby/)
[Chat Example App Using Server-Sent Events](https://robots.thoughtbot.com/chat-example-app-using-server-sent-events)
[websocket之实现简易聊天室(四)](https://www.rails365.net/articles/websocket-zhi-shi-xian-jian-yi-liao-tian-shi-si)
