---
layout: post
title: 'CSRF and CORS'
date: 2016-04-28 08:00
comments: true
categories: 
---
https://segmentfault.com/q/1010000000713614
https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/
https://developer.mozilla.org/zh-TW/docs/HTTP/Access_control_CORS


https://github.com/pillarjs/understanding-csrf
https://ihower.tw/rails4/security.html



CSRF工作方式
```html
<form action="https://my.site.com/me/something-destructive" method="POST">
  <button type="submit">Click here for free money!</button>
</form>

<!-- 也有可能在未經你同意就自動發出POST -->
<a href="http://www.harmless.com/" onclick="
  var f = document.createElement('form');
  f.style.display = 'none';
  this.parentNode.appendChild(f);
  f.method = 'POST';
  f.action = 'http://www.example.com/account/destroy';
  f.submit();
  return false;">To the harmless survey</a>
```

防範方式
0. 會更改到資料的，應該用POST送出，而瀏覽器中只能用form送出POST。
1. 使用JSON APIs：AJAX使用Javascript，並且具同源政策，非同源的request會被server擋，即使是同網域不同port也不會通過。
2. 使CORS無效：如果要讓CORS有效，僅讓`OPTIONS, HEAD, GET`有效即可。但是此方法對form POST無效，因為form不是用Javascript送出，所以不受限同源政策。
3. 不要用POST：因為form只能送`GET, POST`，使用`PUT, PATCH`等方法可以降低被攻擊的機會。
4. 
