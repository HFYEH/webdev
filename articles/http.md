# SSL

[HTTPS科普扫盲帖](https://segmentfault.com/a/1190000004523659)

公私鑰加密的兩個問題：

1. 一般人不會獲取公鑰
2. 伺服器公私鑰的非對稱加密僅保證client->server安全，因為訊息是用公鑰加密，私鑰解密，但是伺服器回傳時若經過代理伺服器，因為server->client的資訊是用私鑰加密的，惡意的代理伺服器就可以透過server公鑰解密，資訊就會外洩。

解法：

1. 證書和CA（頒證書的機構），訪問網站時，伺服器主動回傳證書，內含網站公鑰
2. 既然server公私鑰不能保證傳輸安全，那麽就使用瀏覽器產生的對稱金鑰來加解密，只要瀏覽器在得到證書後取出公鑰，接著生成對稱金鑰並以該公鑰加密回傳給server，server用私鑰解密出對稱金鑰，往後就可以用此對稱金鑰加解密。

但是，證書一樣可以被代理伺服器偽造，而使得client收到的可能是假的證書，資訊又會再次外洩。

瀏覽器有內置主流CA的證書，取得server的CA後，可以透過下列方法驗證證書真偽。

[和安全有关的那些事(非对称加密、数字摘要、数字签名、数字证书、SSL、HTTPS及其他)](http://blog.csdn.net/bluishglc/article/details/7585965)

[SSL/TLS协议运行机制的概述](http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)

[nginx配置ssl加密（单双向认证、部分https）](http://seanlook.com/2015/05/28/nginx-ssl/)

[SSL/TLS原理详解](https://segmentfault.com/a/1190000002554673)

[OpenSSL 与 SSL 数字证书概念贴](https://segmentfault.com/a/1190000002568019)

[基于OpenSSL自建CA和颁发SSL证书](https://segmentfault.com/a/1190000002569859)

[解析傳輸協定與運作流程，網站SSL加密原理簡介](http://www.netadmin.com.tw/article_content.aspx?sn=1106140008)


[Nginx SSL Certificate Installation](https://www.digicert.com/ssl-certificate-installation-nginx.htm)



# Content Security Policy


[An Introduction to Content Security Policy](http://www.html5rocks.com/en/tutorials/security/content-security-policy/)

[Content Security Policy Reference](https://content-security-policy.com/#source_list)
