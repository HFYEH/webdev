# Metaprogramming in Programming Ruby

本篇文章是Programming Ruby第三部份關於meataprogramming的讀書筆記，幫助整理思路。直接進Metaprogramming Ruby感覺比較吃力，有一些觀念是看了這章後才豁然開朗的。如果讀者讀Metaprogramming Ruby碰壁，可以考慮先讀Programming Ruby的這一章。

## Object and Class

Ruby中萬物皆物件（除了block），所謂的`class`也只是class `Class`自己的實例而已。對Ruby來說，class只是物件的一種而已。

`Object`包含一些***flag***，***實例變數***，和關聯到的***class***。

`Class`因為也是`Object`，所以包含上面所有，再加上一些***（實例）方法***和關聯到的***superclass***。

class method其實不存在`class`中，而是存在其產生的`singleton class`中作為實例方法而存在，稍後會提到。

#### self的幾個用途

`self`指的是當前object，如果沒有explicit receiver，`self`不會變化。

1. 用來控制Ruby找其實例變數。
2. 當沒有explicit receiver時，會以`self`作為receiver。
3. 當有explicit receiver時，`self`會被設定為該object。

#### self in class object

當使用class關鍵字時，會將`self`設為此class object，在class定義中，`self`總是被設為此class object。

## Singletons

每當為一個object建立一個實例方法時，它會產生一個anonymous class (singleton class)，object會成為該class的實例，而object原本的class會變成此class的parent class。

由於class也是object，當我們在class內使用`self.xxx`時，也是在為class object建立實例函數，故也會產生新的singleton class，繼承於原本的class，當前的class也會變成該singleton class的實例。

#### Reopen singleton class
```
class << your_object   # 打開your_object的singleton class
  def xxx              # 為your_object定義instance method
  end
end
```
Ruby會禁止你用singleton class建新的實例，這是取名singleton class的原因。

## Module and Mixins

在class裡include module就像是為這個class新增一個superclass。

實踐上，當include module時，Ruby會建一個新暱名的class object，當前class的superclass指向它，而新的暱名class object的superclass指向原class的superclass。此暱名class本身並不帶instance method，要作函數找查時，它會指向module要函數。如此才可以做到隨意插入module至class中而不會造成繼承樹的錯亂。但也有一個問題，就是一旦改動module，所有include此module的都會立即改變。

prepend也是類似，只不過與原class的繼承關係相反。




