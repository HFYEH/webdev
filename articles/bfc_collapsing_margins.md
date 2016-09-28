### 前言

Formatting Context 格式化上下文，是一塊渲染區域中的渲染規則，它決定元素定位方式，和與其他元素的交互作用。Box是CSS布局的基本單位，元素的類型（會有預設的display性質）或附加的display屬性會決定此box要參與哪一種formatting context。

W3C CSS2.1 有BFC (Block Formatting Context)和IFC (Inline Formatting Context)，W3C CSS3 新增了GFC, FFC。

### Block Formatting Context (BFC)

##### 產生條件

display屬性為`block`, `list-item`, `table`的元素



塊級元素是一個獨立的盒子，內部布局不受外部影響，也不會影響到外部布局。有點像是程式語言中的作用域概念，一旦建立起一個塊級元素，就等同於建起一個獨立的作用域。

BFC的特性條列如下：

1. 內部的box會在垂直方向，從上而下排列。
2. Box垂直方向的距離由margin決定，屬於同一BFC的兩相鄰box的margin會重合。
3. 


display屬性為`inline`, `inline-block`, `inline-table`的元素為行內元素，會採用IFC。




[深入理解BFC和Margin Collapse](http://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)

[CSS魔法堂：重新认识Box Model、IFC、BFC和Collapsing margins](http://web.jobbole.com/86060/)

[揭开外边距折叠Collapsing margins的面纱](http://developer.51cto.com/art/201008/219288.htm)
