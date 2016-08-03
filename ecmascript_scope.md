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

#### 執行環境 Execution context
可執行代碼分成三種：Global code, Eval code, Function code
****當執行控制流進入Global code或Function code時，都會創建一個執行環境。而執行一函數時，該執行環境會在棧頂，離開時，會銷毀該環境。****

#### 變量對象 Variable Object
****每一個執行環境都有一個與其相關的變量對象，它記憶了此環境中的的變量和函數。****

一段程式碼如下
```
var name = "Microsoft";

function funcA(){
    var name = "Google";
    alert(name);
}

funcA(); //Google

alert(name); //Microsoft
```

![](http://images.cnblogs.com/cnblogs_com/leoo2sk/201012/201012192354556315.png)

當執行到funcA時，建立一個環境並壓到棧頂，而兩個執行環境各自指向funcA的變數對象和全局變數對象，每個變量對象如同Hash table，保存所有函數和變量。

****this保存的是觸發此環境的對象，arguments保存的是函數的參數（全局執行環境沒有）****

作用域鏈 Scope Chain

上面提到函數中的var會覆蓋全域變量，這個模型怎麽知道何時該用函數域變量，何時該用全局變量？這就涉及到作用域鏈。

假設另一個例子
```
var name = "Microsoft";

function funcA(){
    // var name = "Google";
    alert(name);
}

funcA(); //Microsoft
alert(name); //Microsoft
```
ECMAScript執行模型中每個環境都會關聯一個作用域鏈。可以把作用域鏈想成是一個pointer stack，每個元素都是指向某個變量對象的指針，從棧頂到棧底，指向的對象分別是當前環境的變量對象，上一層的變量對象，依此類推，最後一個指向全局變量對象。下圖是對上圖增加作用域後的樣子。
![](http://images.cnblogs.com/cnblogs_com/leoo2sk/201012/201012192354569281.png)

funcA內部有個特殊的pointer，指向funcA的作用域鏈，funcA的作用域鏈分別指向兩個變量對象。當ECMAScript解釋器尋找一個變量時，會先到scope0指向的變量對象中找，找不到再往scope1指向的變量對象中找。

結論：ECMAScript開始執行時，創建一個全域執行環境和全域變量對象，之後每次進入一個函數，就將該函數的執行環境壓入棧頂，同時創建執行環境的變量對象和作用域鏈，作用域鏈的規則是複製上一層環境的作用域鏈，並將指向當前環境變量對象的指針放到棧頂。跳出函數時，執行環境從棧頂彈出，該變量對象和作用域鏈也同時銷毀。


##解讀閉包和匿名函數


****不能理解閉包關鍵是沒有理解ECMAScript模型****（就是說我）。


結合上面的執行模型，想像一下將一個函數內嵌套另一個函數，並將此函數作為返回值，執行模型為何？

```
var name = "Microsoft";

function funcA(){
    var name = "Google";
    alert(name);
    return function(){
        name = "Facebook";
        alert(name);
    };
}

var o = funcA(); //Google
alert(name); //Microsoft
o(); //Facebook
```































