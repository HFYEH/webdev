參考ihower的[使用 httperf 做網站效能分析](https://ihower.tw/blog/archives/1749)

使用重點：

1. sample數要30以上
2. 兩個標準差即可得到95%的信賴區間

用法

`httperf --server www.example.com --port 80 --uri /api/v1/stories --method GET --num-conns 1000 --add-header="Content-Type:application/json\nProperty1:Value1\nProperty2:Value2\n"`

--num-conns是發出的request總數
--add-header要用\n隔開

壓測，增加--rate和--hog，漸增，看每秒可以承受多少requests

httperf --server theiadev.owlsian.com --port 80 --uri /api/v1/stories/feed --method GET --num-conns 1000 --add-header="Content-Type:application/json\nProperty1:Value1\nProperty2:Value2\n" --rate 150 --hog