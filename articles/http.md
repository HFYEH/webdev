
# SSL

[HTTPS科普扫盲帖](https://segmentfault.com/a/1190000004523659)

伺服器公私鑰的非對稱加密僅保證client->server安全，因為訊息是用公鑰加密，私鑰解密，但是伺服器回傳時，若經過代理伺服器，因為server->client的訊息是用私鑰加密的，惡意的代理伺服器就可以透過server公鑰解密。



[nginx配置ssl加密（单双向认证、部分https）](http://seanlook.com/2015/05/28/nginx-ssl/)

[SSL/TLS原理详解](https://segmentfault.com/a/1190000002554673)

[OpenSSL 与 SSL 数字证书概念贴](https://segmentfault.com/a/1190000002568019)

[基于OpenSSL自建CA和颁发SSL证书](https://segmentfault.com/a/1190000002569859)

[解析傳輸協定與運作流程，網站SSL加密原理簡介](http://www.netadmin.com.tw/article_content.aspx?sn=1106140008)


[Nginx SSL Certificate Installation](https://www.digicert.com/ssl-certificate-installation-nginx.htm)



# Content Security Policy


[An Introduction to Content Security Policy](http://www.html5rocks.com/en/tutorials/security/content-security-policy/)

[Content Security Policy Reference](https://content-security-policy.com/#source_list)
