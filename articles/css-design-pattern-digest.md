這篇文章蒐集一些有關CSS設計模式的文章連結，並加入摘要，方便回憶之用。


## [CSS设计模式之三权分立模式篇](http://www.hicss.net/separation-of-powers-model-in-css-design-patterns/)

CSS三權分立指的是將下列三者分別寫在不同class中，以達到較高的代碼重用性。

1. width/height
2. margin
3. border/padding

文中範例：

```
<!-- html -->
<div class="box mt15 ml10">
  <div class="roundBox">
    市面上我们常常会看到各种各样的设计模式书籍，Java设计模式、C#设计模式、Ruby设计模式等等。
  </div>
</div>

// css
.box{width:300px; height:80px;}
.roundBox{padding:10px 15px; border:1px solid #ccc;}

.mt10{margin-top:10px}
.mt15{margin-top:15px}
.ml10{margin-left:10px}
.ml15{margin-left:15px}
...
```

1. box實際的大小是width+padding+border三者藕合在一起的，造成修改不易。
2. 把width和height放在外層，不另加border/padding，此外層box標示box實際的大小。
3. 犧牲一點點效能，在內層建立一個新的tag，標示border和padding，border和padding本質上幾乎是一樣的。
4. 往後若須修改padding，因為已經與width解藕合，所以只要直接改內層的css即可。
5. 先定義好一系列的margin，同一個box如果須要不同的margin只要抓進來用即可，完全不須重寫css。




[Bootstrap 栅格系统的精妙之处](http://get.ftqq.com/6195.get)

[逐行阅读 Bootstrap 源码](https://www.loyalsoldier.me/understand-bootstrap-source-code-line-by-line/)
