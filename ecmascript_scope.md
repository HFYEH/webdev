[原文](http://www.cnblogs.com/leoo2sk/archive/2010/12/19/ecmascript-scope.html#comment_tip)（作者张洋）


## 例子

在多數程式語言中，變數是有塊級作用域的。如C
```
// C
int sum = 0;

for(int i = 1; i <=10; i++){
    sum += i;
}

int j = i; //會出錯，因為i僅在block中可見

```
但在JavaScript中則沒有塊級作用域
```
// JavaScript
int sum = 0;

for (var i=1; i<=10; i++){
    sum += i;
}

console.log(i); // 11
```
結論：ECMAScript中的作用域分為全局作用域和函數作用域，全局作用域是全局可見的，在函數中沒有使用var的變量也是全局變量，只有使用var的變數才是函數作用域變量，**函數作用域變量在函數作用域中會覆蓋同名的全局作用域變量**。全局作用域變量在整個腳本中可見，僅在被覆蓋的函數作用域中被覆蓋，而函數作用域變量只在函數內可見，除了被內嵌套函數中同名函數覆蓋的區域外。

## ECMAScirpt作用域模型


