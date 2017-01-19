### 前言

Formatting Context 格式化上下文，是一塊渲染區域中的渲染規則，它決定元素定位方式，和與其他元素的交互作用。它在正常的文檔流中另外開一種排版格式，並具備某些特性，比如可包含float元素，使得排版更有彈性。

Box是CSS布局的基本單位，CSS只認box而不認元素的，元素的類型（會有預設的display性質）或附加的display屬性會決定此元素生成怎的box類型，box類型會決定該box要參與哪一種formatting context。

W3C CSS2.1 有BFC (Block Formatting Context)和IFC (Inline Formatting Context)，W3C CSS3 新增了GFC, FFC，此文暫不涵蓋。

##### 名詞解釋

Box：就是常說的盒模型的box，有幾種類型，以display決定，有block-level box和inline box。Box是被元素生成的。

Block-level element：為元素設置`display`為`block`, `list-item`, `table`的元素，就是塊級元素。

Block-level box：每個塊級元素至少會生成一個block-level box。塊級盒會參與BFC。

Block containing box：塊容器盒只包含其他block-level box或inline-level box。

Block box：同時是block-level box和block containing box的稱為塊盒。

![](https://developer.mozilla.org/@api/deki/files/5995/=venn_blocks.png)

Inline-level element：為元素設置`display`為`inline`,`inline-block`,`inline-table`的元素，就是行內元素。

Inline-level box：行內級元素會行內級盒，但不是所有的行內級盒都會參加IFC。

Inline-box：參與IFC的行內級盒稱為行內盒。只有`display`為`inline`的inline-level box會參與IFC，而`display`為`inline-block`,`inline-table`和可替換行內元素生成的盒，不能拆分多個盒，不參與IFC。

![](https://developer.mozilla.org/@api/deki/files/6008/=venn_inlines.png)

##### 文檔流

Normal flow：在BFC中垂直排列，在IFC中水平排列。`position`為`static`或`relative`，且`float`為`none`時會觸發。

Float：處於當前行的開頭或末尾，會使得normal flow圍繞在其周圍，直至設置clear為止。`position`為`state`或`relative`，`float`不為`none`時會觸發。

Absolute positioning：從normal flow中被移除，且不影響normal flow布局。`position`為`absolute`或`fixed`時觸發。

參考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Visual_formatting_model)。

### Block Formatting Context (BFC)

##### 產生條件

一共有四種。一個元素若滿足任何一個條件，將生成一個BFC。

（根元素也會產生一個BFC）

1. `float`不為`none`（只要是浮動元素就會生成BFC）
2. `position`為`absolute`或`fixed`（絕對位置元素也會生成BFC）
3. `display`為`inline-block`,`table-cell`, `table-caption`(CSS3新增`flex`, `inline-flex`，`table`則會間接產生BFC）
4. overflow不為visible

BFC是一個獨立的區域，內部布局不受外部影響，也不會影響到外部布局。有點像是程式語言中的作用域概念，一旦建立起一個塊級元素，就等同於建起一個獨立的作用域。

這四種生成BFC的方式中，以overflow: hidden最為常用，副作用最小。

##### BFC內部的布局規則

1. 內部的box會在垂直方向，從上而下排列。（由上而下）
2. 內部的Box垂直方向的距離由margin決定，***屬於同一BFC的兩相鄰box的margin***會疊加。（此段容後細說）
3. 內部box的margin-left會與BFC的border-left碰觸，浮動的內部Box亦然（以float: left為例）。（BFC內元素預設皆靠左對齊）
4. BFC的區域不會與float元素重疊。（也就是去除浮動，可用於布局）
5. 計算BFC高度時，內部的float元素會列入計算。（BFC可以包含住float元素）

##### 功用

* 外邊距合併例子

上面2提到，***屬於同一BFC的兩相鄰box的margin***會疊加。此處的相鄰有更細緻的條件：

1. 一般的文檔流中的block-level box，不是float和絕對定位（不然就自己產生BFC了）才有可能發生
2. block-level box間沒有clearance，也沒有padding或border隔開
3. 上邊距或下邊距才會發生合併
4. 承上，父元素和滿足上述條件的first-child元素的margin會合併
5. 承上，滿足上述條件的子元素和sibling元素的margin會合併
6. 承上，height為auto的父元素和滿足上述條件的last-child元素的margin會合併
7. 最小高度和高度為0，且自身沒有建立BFC，且沒有一般文檔流的子元素，因為上邊距和下邊距間沒有隔開，也會合併


<p data-height="265" data-theme-id="0" data-slug-hash="xELRJB" data-default-tab="html" data-user="sharefun" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/sharefun/pen/xELRJB/">BFC - collapse margin</a> by sharefun (<a href="http://codepen.io/sharefun">@sharefun</a>) on <a href="http://codepen.io">CodePen</a>.</p><script async src="//assets.codepen.io/assets/embed/ei.js"></script>

* 布局

<p data-height="265" data-theme-id="0" data-slug-hash="xEXwJG" data-default-tab="html,result" data-user="sharefun" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/sharefun/pen/xEXwJG/">BFC - 布局</a> by sharefun (<a href="http://codepen.io/sharefun">@sharefun</a>) on <a href="http://codepen.io">CodePen</a>.</p><script async src="//assets.codepen.io/assets/embed/ei.js"></script>


* 容納浮動元素

<p data-height="265" data-theme-id="0" data-slug-hash="ZpXbdz" data-default-tab="html,result" data-user="sharefun" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/sharefun/pen/ZpXbdz/">BFC - 容納浮動元素</a> by sharefun (<a href="http://codepen.io/sharefun">@sharefun</a>) on <a href="http://codepen.io">CodePen</a>.</p><script async src="//assets.codepen.io/assets/embed/ei.js"></script>


浮動和絕對定位元素，因為脫離了當前的文檔流，所以margin沒有和別的元素相鄰，又因為產了BFC，所以不會和其他元素的外邊距合併。

浮動和絕對定位元素脫離文檔流之後，後面的sibling元素會接上一般文檔流，所以絕對定位元素上下的元素也有可能發生外邊距合併，須注意。

閉合浮動元素會產生clearance，這會使得閉合浮動元素的border-top總是緊貼著最後一個浮動元素的margin-bottom，而且為閉合浮動元素設置margin-top是無效的，詳見[此篇](http://www.imooc.com/article/9723)。


## Inline Formatting Context (IFC)

IFC會生成line box，為排版基本單位，line box內部的Box水平排列，水平方向上的寬度會得到保留。如果寬度大於containing box的話，會自動換行。每一行都會生成一個line box。

## 參考資料：

[细说 CSS margin](https://blog.coding.net/blog/css-margin)

[深入理解BFC和Margin Collapse](http://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)

[CSS魔法堂：重新认识Box Model、IFC、BFC和Collapsing margins](https://segmentfault.com/a/1190000004625635)

[CSS之BFC详解](http://www.html-js.com/article/1866)

[CSS 三种定位方式以及格式化上下文详解](http://www.html5jscss.com/box-context.html)

[我知道你不知道的负Margin](http://www.hicss.net/i-know-you-do-not-know-the-negative-margin/)

