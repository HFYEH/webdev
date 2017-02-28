--url 指定要抓取的網址

-A 指定Agent名稱

-b 指定使用Cookie檔案

-c 指定建立Cookie檔案

-d 指定POST的資料

-L 自動重新導向

-s 不顯示處理的訊息

-o 指定輸出檔案

curl -o <file_name> url
curl -L url 自動跳轉
curl -i url 顯示包含header的response
curl -I url 只顯示header
curl -v url 顯示整個http的所有過程

curl -X POST 使用選擇方法
curl -X POST --data "parameter=hihi" url 加上參數
curl -X POST --data-urlencode "parameter=hihi" url 使用urlencoded

curl --user-agent "[agent]" url 設定user agent
curl --cookie "xxx=ooo" url 設定cookie
curl -c cookie_file url 可將server返回的cookie存到cookie_file
curl -b cookie_file url 可將cookie_file內的cookie帶入發送出去
curl --header "Content-Type="application/json" url 帶header的request
curl --user name:password url 簡單的http認證

[curl测试网站](http://www.zhengdazhi.com/archives/788)