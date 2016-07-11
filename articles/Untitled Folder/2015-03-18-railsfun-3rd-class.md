---
layout: post
title: 'RailsFun 3rd class'
date: 2015-03-18 10:49
comments: true
categories: [railsfun]
---
#3.HTML & CSS & RWD

教學大綱
HTML
w3c，標記語言（有頭有尾 & 單一），與 xml 的關係（ meta data：<!DOCTYPE html>）
html / head / body
載入順序，js / css 擺放位置與源由（demo : 由js取dom）
何謂 CDN 與重要性（瀏覽器 connection 數量限制 vs domain name [ 6 ~ 8 ]）
重點：重視語意 / 不得缺損 / 重視縮排
tag 的 overview，語意解釋
所謂的 SEO：tag 的選用，attr 的設定，網址 / 檔名所影響關係
frame / iframe 與 target 關連命名
#id 錨點的真義與使用
data-attribute的命名方式
相對 / 絕對 / 不定 protocol 網址連結

CSS
結構：css file 與 inline-stylehttp://www.quickbid.com.tw

選擇器1：class / id
原型與種類：display
大小與內外距的關係：width / height / border / padding / margin
行高：line-height / vertical-align
背景：background：background-position（hover）
排列方式1：float / clean / .clearfix (:after)
前置與後置：:before / :after
WebFont => icomoon.io (:before)
排列方式2：position：static / relative / absolute / fixed
選擇器2：多層化：http://flukeout.github.io/ < 把這玩破
覆蓋策略1：打牌策略 inline > id > class > tag，鬼牌 !important（噗浪案例）
覆蓋策略2：RWD實作
<meta name="viewport" content="width=device-width, initial-scale=1"/>
@media all and (min-width: N px),(max-width: M px){ … }

跨瀏覽器策略（增加css vs 不建議用的css hack）
Bootstrap（使用法：Layout / CSS / JavaScript，Grid System％實現）
其他：screen / print 的分別

垃圾話
hmmm...這邊的東西比較輕鬆就是了，但是觀念很深...（JokerCatz）
A...乾，連舊資料都有人幫我debug是哪招，but感謝就是X”DD（JokerCatz）


教學部分內容（舊資料...）


HTML 教學
基本與架構
html是 w3c 製定的一種類似 xml 的標記 (tag) 語言，語法很簡單，通常是有頭有尾，或是單一表示
<a href="#index">link</a> #有頭有尾
<img src="url.png" /> #自己一個
基本結構很簡單，本身是樹狀結構，從 html 開始，類似
<!DOCTYPE html>
<html>
  <head>
	<title>Hello World</title>
  </head>
  <body>
	<h1>Hi!</h1>
  </body>
</html>
所以 html 包 head / body，head 包 title，body 包 h1，一層一層包下去

這邊注意的地方是，html 本身是 xml 的延伸l，而舊版 (html4.n-) 的和 html5 最大的差別在於第一行，也就是
<!DOCTYPE html>
在 xml 本身很重視 metadata，也就是用一份 xml(meta) 來形容另外一份 xml(data)，所以 html 本身也有相對應的 metadata，而使用 HTML5 與舊版的分別就在於第一行地宣告上面，所以沒有宣告 DOCTYPE 可能會沒辦法使用新的功能，或是有表現模式不一樣的問題，此部分詳細請見類似這邊，宣告不同的 metadata 就會有不同的表現和運作模式

之後就是html / head / body三大元素，大概描述一下

<html> 以生物分類法而言（國中背的『界門綱目科屬種』），它屬於『域』，也就是在『界』的上面，基本上沒有任何  tag 能與它同層，除了表述它的DOCTYPE之外

<head> 裡面放的是這份html內的所有基本資料，類似 <meta> / <title> / <link> 系列的 tag，建議將 CSS 宣告放這邊，因為 CSS 必須先被讀取(見 HTML 載入順序)

<body> 裡面放的是這份 HTML 內所有的內文，建議將 JavaScript 之類的宣告放在 body 的最後面(見 HTML 載入順序)

以下純補遺
很抱歉的，tag 基本語義與使用方式，還有 attribute 這邊你應該要去看書了，坊間 HTML 的書很多，以下只是針對補遺的部分
還是哪天等人來幫我補足...

1.重視語義
HTML 非常重視語義，類似 <p> 是段落 (paragraph)，一個文章的區塊，而 <span> 是片段文字，或是行內文字或區塊的集合，所以你不應該用 <span> 去包 <p>，如果你只因為外觀的因素而放棄重視語義本身，你往後的學習甚至是成品將會非常的差勁，簡單的來說找個男人做女生的工作，或是相反，雖然可以經過化妝等方式包裝，但是本質其實是不一樣的，如果從根本就爛掉了，神仙也難救，就別去說後面的美工 / CSS / JS 之類的工作了，因為一切白談

然而知道語義後，後面長得怎樣就用 CSS / JS 全部解決即可，最多就是多一層少一層排哪裡的問題而已，請務必不要本末倒置，這是大忌且切記

2.不得缺損
例如有頭有尾的 tag，結果尾巴少打了，就會變成不可預期的狀態，所以不管怎樣的情況都請避免掉這狀況，甚至是養成習慣，類似先打上層的頭尾，才打下層的內容，而非打了一堆頭，而忘記補尾，或是尾多打了

3.重視縮排
這部分是方便除錯，也方便檢查上面兩項的良好習慣，請一定要重視，通常會是 4 或 2 個 space (空白) 居多，而部分使用 IDE 的團隊會使用 tab 來分隔(通常1 tab 寬度 = 2 or 4 個 space)，切記勿把 tab 與space混用

HTML 載入順序
HTML 上，我們稱標記語法為 tag  (用  <> 標記的標誌)，但實際使用時叫做 Document Object Model (DOM)，也就類似樹的節點，而且是順序的，寫在前面比較快載入，後面則比較慢，而與 CSS 混用實則是

HTML + JS[inline] + CSS[inline] => CSS[link] => 顯示 => JS[link]

原因大概是 CSS 必須全部被讀取完了，HTML 才有完整的樣貌，所以 CSS 必須讀完 HTML 才會完整表現，而JS 如果寫在 HTML 的 dom(A) 前面，但是該 JS 要抓 dom(A) 是抓不到的，但寫在後面則可以，所以 CSS 會希望寫在越前面越好，JS 大多處理dom，所以會希望越後面越好

而另外一個名詞叫做 CDN (content delivery network)，CDN 只解決一個問題：快速載入外部檔案，原因在於瀏覽器對同一個 domain 有連線數量的限制，類似一個網頁內同時有過多且分善的圖片 / CSS / JS / 影片在同一domain時，會排隊依序下載，但切了不同的 CDN (不同domain) 則能打破限制，且 CDN 可以針對 client 所在的地區做優化，類似台灣連台灣的 CDN 而非連去俄羅斯



HTML 本身基礎不多，其餘的大多都是深度應用而已就是
（簡單的來說就是想到才補）


CSS 教學
你應該先看完 HTML 教學才能看這邊，就算你學會了 HTML，也應該再去看一次:)
這邊會教完所有 CSS 的基本，有順序的，順便附上 RWD 的心法大全

CSS 的大綱其實只有很少的幾點，掌握這幾點你就能成為 CSS 大師，先表述大綱，後詳述語法

1.所占空間
HTML 或 CSS 設計通常叫做 box design，而 CSS 重要的也是 box model，意思就是設計每個小塊的區塊：所占大小 / 表現模式 / 所在位置 / 間距，其餘就是上色和內容放啥的問題而已，然而如果不知道控制大小，就不知道為啥會排得亂糟糟，所以請先了解所占空間為何物

2.選擇器
知道怎樣設計後，就是取到正確的 dom，把它變成該表現的方式，而選擇器和 class / id 的命名成就了你的CSS寫得內容的重複次數，而做得多錯得多，所以盡量命名得好然後讓相同一段CSS可被重複使用，且易於管理

3.覆蓋守則
會上面1&2後，不知道如何覆蓋已存在的 CSS，則會陷入鬼擋牆，無限循環且怨恨 CSS，或是會亂出鬼牌，無法隨心所欲的完成想做的事情，浪費時間在測試上面
