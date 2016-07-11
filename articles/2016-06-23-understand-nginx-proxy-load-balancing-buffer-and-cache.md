---
layout: post
title: '理解Nginx代理，負載平衡，緩衝和緩存'
date: 2016-06-23 02:09
comments: true
categories: 
---
[DigitalOcean的原文](https://www.digitalocean.com/community/tutorials/understanding-nginx-http-proxying-load-balancing-buffering-and-caching)


## 解構HTTP proxy pass（正向代理）

```shell
# server
location /match/here {
    proxy_pass http://example.com;
}
```
如果client發出的uri包含/match/here，就會被代理到上游(upstream) server example.com。
舉例：/match/here/please的request會被送至http://example/match/here/please

```shell
# server
location /match/here {
    proxy_pass http://example.com/new/prefix;
}
```
此例下，/match/here/please會被送至http://example.com/new/prefix/please，部份的uri會被取代掉，請記住這一點。

不過，當location pattern無法被確定時（因此也不知到要取代哪個部份），client發出的請求就會整串塞到proxy_pass domain name後面。


## Nginx如何處理Header
如果要讓upstream server適切的處理被代理伺服器轉發的request，除了uri之外，還有一些東西是必須更改的。
從Nginx代理出去的request會與直接從client直接發出的request看起來不太一樣。
主要是伴隨request的header。

* Nginx丟棄空的headers。攜帶空的header只會使request更為腫脹。=>如果我們不想送的header就要設成空字串。
* Nginx預設會把包含`_` 的header視為無效。Nginx會將其移除。如果你希望Nginx把這些headers當成有效的，你可以設定`underscores_in_headers directive` 為 "on"。否則你的header將永遠無法送至目標server。
* `Host` header會把`$proxy_host`變數覆寫掉。其內容為upstream server的IP或domain name，如同proxy_pass設定的那樣。=>最重要的header之一，`$proxy_host`會直接取用proxy_pass。
* `Connection` header會被改為"close"。這是用來標註有關特別連線所用的資訊。在此例中，Nginx設定其為"close"去指示upstream server這個連線在response該request後就會close。upstream server不應該預期該連線繼續保持。

最常見的"Host" header為：
* $proxy_host: 這會將"Host" header設為從proxy_pass定義中取出的domain name或IP 和 port。此為預設而且從Nginx角度看來是安全的，但通常並不是被代理的伺服器需要的資訊。
* $http_host: 將"Host" header設定為從client request 的 "Host" header。此 headers 由client發出，因此永遠是Nginx可得的變數。這個變數以 $http_ 為開頭，後面接小寫的 header name，並且以`-`連接，以取代`_`。雖然 $http_host 大部份時間可以成功運作，但當client request沒有一個有效的"Host" header，這種設定就會失敗。
* $host: This variable is set, in order of preference to: the host name from the request line itself, the "Host" header from the client request, or the server name matching the request.

在多數情況，你須要設定"Host" header為$host變數。此設定最具彈性，而且通常可以讓被代理的伺服器盡可能可以精準的填入"Host" header。

## 設定或重設Headers

可以使用`proxy_set_header`調整或設定headers。舉例，要加入代理的request中常見額外的headers可以這樣設定：

```shell
# server
location /match/here {
    proxy_set_header HOST $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    proxy_pass http://example.com/new/prefix;
}
```
我們把"Host" header設定為 $host 變數。
`X-Forwarded-Proto` header 給被代理的伺服器有關原始cient request的scheme。
`X-Real-IP` 設定為client的IP。
`X-Forwarded-For` 包含client被代理到這點上的所有IP，此例爭我們設定為`$proxy_add_x_forwarded_for`變數。此變數取用原本的`X-Forwarded-For`再加上Nginx自己的IP於其後。

當然，我們可以將`proxy_set_header`移出server或http文本，使它們被適用於更多location：

```shell
# http
proxy_set_header HOST $host;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_Header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

location /match/here {
    proxy_pass http://example.com/new/prefix;
}

location /different/match {
    proxy_pass http://example.com;
}
```

## 定義Upstream文本for Load Balancing Proxied Connections

前例中，我們示範如何做一簡單的http proxy至單一的後端server。Nginx允許我們輕易scale次配置至整個後端server的pools，我們都可以送request出去。

我們可以設定upstream去定義server pool。這種配置假設任一列出來的server都可以處理client request。
**Upstream必須被設定在http內文裡**

看看例子：

```shell
# http
upstream backend_hosts {
    server host1.example.com;
    server host2.example.com;
    server host3.example.com;
}

server {
    listen 80;
    server_name example.com;

    location /proxy-me {
        proxy_pass http://backend_hosts;
    }
}
```

此例我們設定了upstream 內文為名稱為 backend_hosts。一旦定義好，這個名字可以在代理傳遞中被取得，就好像它是一個正常的domain name一樣。可以看到，任何example.com/proxy-me/...會被傳至我們預先定義好的backend_hosts pool，host會被演算法選擇出來。預設上只是簡單的依序發給不同的host而已。


### 更變 Upstream Balancing Algorithm

我們可以更改balancing的方式
```shell
# http
upstream backend_hosts {

    least_conn;

    server host1.example.com;
    server host2.example.com;
    server host3.example.com;
}
```

* (round robin): 預設值。每個server會依序接到新進來的request。
* least_conn: 新的連線總是會被送到有最少active connections的後端。在connection至後端會持續一段時間的時候特別有用。
* ip_hash: 這個平衡演算法依據client IP把request分到不同的server。前三個位元組被用作key去決定那個server要處理此request。結果是client傾向於被同一個server服務，可以幫助保持session一致。
* hash: 此平衡演算主要使用memecached代理。Server依據隨機的hash key被區分。可以是文本，變數或組合。這是唯一須要使用者提供資料作為key。

對於hash方法，你必須提供key

```
# http
upstream backend_hosts {

    hash $remote_addr$remote_port consistent;

    server host1.example.com;
    server host2.example.com;
    server host3.example.com;
}
```
上例會依照client IP和PORT發送request。我們加入一個可選的參數consistent，會植入ketama consistent hashing algorithm。基本上，這表示如果你的upstream server改變了，對你的cache衝擊會最小。

### 設定權重

預設的權重是相同的。如果想要設定權重可以如下所示：
```
# http

upstream backend_hosts {
    server host1.example.com weight=3;
    server host2.example.com;
    server host3.example.com;
}
```

因為預設值為1，host1.example.com會收到其他server三倍量的request。


## 使用 Buffer 解放後端server

許多人關心新增server到process中會如何影響代理的效能。在多數情況下，這個可以被Nginx的 buffering和caching能力緩解。

代理到其他server，兩種connenction會影響client的經驗：

1. Clent到 Nginx 的 connection。
2. Nginx到 backend server 的connection。

Nginx 能針對你想要最佳化的connection去調整其行為。

沒有buffer，資料會從被代理server送出，緊接著送到client。如果client被假設很快，buffering可以關閉以使得client可以盡快得到資料。有buffer的話，Nginx代理將會暫存後端的response之後才餵資料給client。如果client很慢，這允許Nginx可以較快的關閉與後端的連線。

Nginx 預設有buffering因為client傾向於有各種不同的連線速度。我們可以使用下述方法調整buffering行為。這些可以被設定在http，server，或location內文。注意，sizing在每次request都被設定，所以增加其值，在client request很多的時候會影響效能：

* proxy_buffering: 此項會將目前內文和子內文開啟buffering，預設為開啟。
* proxy_buffers: 此項控制代理的response的buffer的數量（第一個參數）和大小（第二個參數）。預設是8個buffer，大小為4k或8k。增加buffer數可以允許你buffer更多資訊。
* proxy_buffer_size: 最初部份的 response，包含headers是被分開buffer在不同於response的地方。此項設定此部份的response的buffer大小。預設，與proxy_buffer的大小相同，但因為這是用來設定header的資訊，通常可以設小一點。
* proxy_busy_buffers_size: This directive sets the maximum size of buffers that can be marked "client-ready" and thus busy. While a client can only read the data from one buffer at a time, buffers are placed in a queue to send to the client in bunches. This directive controls the size of the buffer space allowed to be in this state.
* proxy_max_temp_file_size: This is the maximum size, per request, for a temporary file on disk. These are created when the upstream response is too large to fit into a buffer.
* proxy_temp_file_write_size: This is the amount of data Nginx will write to the temporary file at one time when the proxied server's response is too large for the configured buffers.
* proxy_temp_path: This is the path to the area on disk where Nginx should store any temporary files when the response from the upstream server cannot fit into the configured buffers.


如你所見，Nginx提供一些微調buffering的選項。大多數時候你不須要擔心這些設定，但可以調整某些參數是很有用的。也許最有用的是 proxy_buffers 和 proxy_buffer_size。

下例中，我對單一的upstream request增加proxy buffer的數量，並且減少儲存header的buffer：

```shell
# server

proxy_buffering on;
proxy_buffer_size 1k;
proxy_buffers 24 4k;
proxy_busy_buffers_size 8k;
proxy_max_temp_file_size 2048m;
proxy_temp_file_write_size 32k;

location / {
    proxy_pass http://example.com;
}
```

相反的，如果你有很快的client，你想要馬上把資料送過去，你可以將buffering完全關閉。如果upstream 比client快的話，Nginx 仍會使用buffer，但它會立刻嘗試把資料沖出到client而不是等buffer。如果client 很慢，這會使upstream連線保持開啟直到client接到為止。當buffering關閉，只有被proxy_buffer_size定義的buffer會被用到：

```shell
# server

proxy_buffering off;
proxy_buffer_size 4k;

location / {
    proxy_pass http://example.com;
}
```


未完...
