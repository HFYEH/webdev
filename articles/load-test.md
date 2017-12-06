
# 壓測

參考ihower的[使用 httperf 做網站效能分析](https://ihower.tw/blog/archives/1749)

使用重點：

1. sample數要30以上
2. 兩個標準差即可得到95%的信賴區間

用法

`httperf --server www.example.com --port 80 --uri /api/v1/stories --method GET --num-conns 1000 --add-header="Content-Type:application/json\nProperty1:Value1\nProperty2:Value2\n"`

--num-conns是發出的request總數
--add-header要用\n隔開

壓測，增加--rate和--hog，漸增，看每秒可以承受多少requests

httperf --server theiadev.owlsian.com --port 443 --ssl --uri /api/v1/stories/feed --method GET --num-conns 1000 --add-header="Content-Type:application/json\nProperty1:Value1\nProperty2:Value2\n" --rate 150 --hog --timeout 5

另外在[How to Generate Millions of HTTP Requests](http://dak1n1.com/blog/14-http-load-generate/)這篇文章中，作者建議使用Tsung，而且說ab的能力太差，以下是Tsung用法

中文說明：[Tsung load test](http://longfamily.pixnet.net/blog/post/332114443-tsung-load-test)


安裝缺少的perl module

Upgrade CPAN

`sudo perl -MCPAN -e 'install Bundle::CPAN'`

`sudo perl -MCPAN -e 'install Module::Template'` or `sudo cpan Template`


Tsung執行是要吃xml設定檔的，安裝完之後，對不同的測試有一些範例檔可用，位置在`/usr/share/doc/tsung/examples`，可以複製到當前目錄使用，執行時使用`tsung -f config.xml start`，若不帶 f，則預設會去找`~/.tsung/tsung.xml`，執行完會存在`~/.tsung/log`下

報表：
mac: `perl /usr/local/lib/tsung/bin/tsung_stats.pl`

ubuntu: `/usr/lib/tsung/bin/tsung_stats.pl`

[Tsung Doc](http://tsung.erlang-projects.org/user_manual/index.html)
[Tsung 報告項目解釋](http://www.voidcn.com/article/p-dagfzaoo-bda.html)