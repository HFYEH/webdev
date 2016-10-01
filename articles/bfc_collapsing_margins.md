### 前言

Formatting Context 格式化上下文，是一塊渲染區域中的渲染規則，它決定元素定位方式，和與其他元素的交互作用。Box是CSS布局的基本單位，元素的類型（會有預設的display性質）或附加的display屬性會決定此box要參與哪一種formatting context。

W3C CSS2.1 有BFC (Block Formatting Context)和IFC (Inline Formatting Context)，W3C CSS3 新增了GFC, FFC。

### Block Formatting Context (BFC)

##### 產生條件

一共有四種。一個元素若滿足任何一個條件，將生成一個BFC。

1. `float`不為`none`
2. `position`為`absolute`或`fixed`
3. `display`為 `inline-block`, `table-cell`, `table-caption`, `flex`, `inline-flex`
4. overflow不為visible

塊級元素是一個獨立的盒子，內部布局不受外部影響，也不會影響到外部布局。有點像是程式語言中的作用域概念，一旦建立起一個塊級元素，就等同於建起一個獨立的作用域。

這四種生成BFC的方式中，以overflow: hidden最為常用，副作用最小。另外可以在外層加border或padding，或將內層第一個block元素做成BFC。

##### BFC布局規則

1. 內部的box會在垂直方向，從上而下排列。（由上而下）
2. 內部的Box垂直方向的距離由margin決定，***屬於同一BFC***的兩相鄰box的margin會疊加。（元素和其first-child或元素和其sibline皆算是相鄰，可用此特性解外邊距合併）
3. 內部的Box的margin-left會與外部的border-left碰觸，浮動的內部Box亦然。（靠左對齊）
4. BFC的區域不會與float元素重疊。（也就是去除浮動，可用於布局）
5. 計算BFC高度時，float元素會列入計算。

##### 功用

外邊距合併例子

<p data-height="265" data-theme-id="0" data-slug-hash="xELRJB" data-default-tab="html" data-user="sharefun" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/sharefun/pen/xELRJB/">BFC - collapse margin</a> by sharefun (<a href="http://codepen.io/sharefun">@sharefun</a>) on <a href="http://codepen.io">CodePen</a>.</p><script async src="//assets.codepen.io/assets/embed/ei.js"></script>

2. 布局

3. 清除浮動計算高度

## Inline Formatting Context (IFC)

display屬性為`inline`, `inline-block`, `inline-table`的元素為行內元素，會採用IFC。




[深入理解BFC和Margin Collapse](http://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)

[CSS魔法堂：重新认识Box Model、IFC、BFC和Collapsing margins](http://web.jobbole.com/86060/)

[揭开外边距折叠Collapsing margins的面纱](http://developer.51cto.com/art/201008/219288.htm)
