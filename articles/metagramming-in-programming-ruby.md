# Metaprogramming in Programming Ruby

本篇文章是Programming Ruby第三部份關於meataprogramming的讀書筆記，幫助整理思路。直接進Metaprogramming Ruby感覺比較吃力，有一些觀念是看了這章後才豁然開朗的。如果讀者讀Metaprogramming Ruby碰壁，可以考慮先讀Programming Ruby的這一章。

## Object and Class

Ruby中萬物皆物件（除了block），所謂的`Class`也只是class `Class`自己的實例而已。但物件和類別的內容是不同的。

`Object`包含一些***flag***，***實例變數***，和關聯到的***class***。

`Class`因為也是`Object`，所以包含上面所有，再加上一些***（實例）方法***和關聯到的***superclass***。

class method其實不存在`Class`中，而是存在其產生的`singleton class`中作為實例方法而存在，稍後會提到。

#### self的幾個用途

`self`指的是當前object，如果沒有explicit receiver，`self`不會變化。

1. 用來控制Ruby找其實例變數。
2. 當沒有explicit receiver時，會以`self`作為receiver。
3. 當有explicit receiver時，`self`會被設定為該object。

#### self in class object

當使用class關鍵字時，會將`self`設為此class object，在class定義中，`self`總是被設為此class object。

## Singletons

每當為一個object建立一個實例方法時，它會產生一個anonymous class (singleton class)，object會成為該class的實例，而object原本的class會變成此class的parent class。

由於Class也是object，當我們在class內使用`self.xxx`時，也是在為class object建立實例函數，故也會產生新的singleton class，繼承於原本的class，當前的class也會變成該singleton class的實例。


