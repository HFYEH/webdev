[原文](http://www.cnblogs.com/leoo2sk/archive/2010/12/19/ecmascript-scope.html#comment_tip)（作者张洋）


[JavaScript: The core](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/)

# 前言

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

# ECMAScirpt作用域模型

## 執行上下文棧

由執行上下文堆疊而成，每次調用新的上下文，就會把新的上下文壓到棧頂。

## 執行上下文 Execution context
可執行代碼分成三種：Global code, Eval code, Function code

****當執行控制流進入Global code或Function code時，都會暫停當前的執行上下文，創建一個新執行上下文（此時調用新上下文的叫caller，被調用的叫callee），callee執行其內容，直到返回為止。而執行一函數時，該執行上下文會在棧頂，離開時，會銷毀該環境。****

執行上下文(EC)可以被當作一個簡單的對象，其屬性包含一個variable object，一個this，和scope chain。

### 變量對象 Variable Object
****每一個EC都有一個與其相關的變量對象(varaible object)，它存放了此上下文中定義過的變量和函數（只有函數宣告，不包含函數表達式）。****這個對象在不同的EC是不同的，如在global環境就是global object本身。而函數會產生新的作用愈域，eval的variable object使用global或者caller的varaible object。

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

當執行到funcA時，建立一個環境並壓到棧頂，而兩個執行環境各自指向funcA的變數對象和全局變數對象，每個variable object如同Hash table，保存所有函數和變量。

當函數被caller調用，會產生一個特殊的activation object，它會有正式參數和arguments對象。這個activation object就會作為函數的varaible object。

### 作用域鏈 Scope Chain

上面提到函數中的var會覆蓋全域變量，這個模型怎麽知道何時該用函數域變量，何時該用全局變量？這就涉及到作用域鏈。

作用域鏈包含其調用鏈的variable object和當前的variable object。

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
ECMAScript執行模型中每個環境都會關聯一個作用域鏈。可以把作用域鏈想成是一個pointer stack，每個元素都是指向某個varaible object的指針，從棧頂到棧底，指向的對象分別是當前環境的variable object，父EC的variable object，依此類推，最後一個指向全局varaible object。下圖是對上圖增加作用域後的樣子。
![](http://images.cnblogs.com/cnblogs_com/leoo2sk/201012/201012192354569281.png)

funcA內部有個pointer，指向funcA的作用域鏈，funcA的作用域鏈分別指向兩個變量對象。當ECMAScript解釋器尋找一個變量時，會先到scope0指向的變量對象中找，找不到再往scope1指向的變量對象中找。

這張圖有點誤導，真正的且實現在ES5上的，是在每個activation object上都有一個屬性[[scope]]指向其caller EC 的 varaible object。不過以array來表現是合理且容易理解的。另外，`catch clause`和`with object`可以動態的增添作用域鏈。

結論：ECMAScript開始執行時，創建一個全域EC和全域variable object，之後每次進入一個函數，就將該函數的EC壓入棧頂，同時創建EC的variable object和作用域鏈，****作用域鏈的規則是複製上一層環境的作用域鏈，並將指向當前環境變量對象的指針放到棧頂****。尋找變量時，先在當前的activation object找，找不到就會從其scope chain中開始找。跳出函數時，執行環境從棧頂彈出，該變量對象和作用域鏈也同時銷毀。

## 解讀閉包和匿名函數

### functional arguments & functional value

JavaScript的函數可以當作變量傳遞，可以傳入當函數參數，也可以當函數返回值。把函數當參數代入另一個函數時，稱作functional arguments，把函數當返回值時，稱作functional value。

為了在函數內的activation variable找不到變量時，為了使其可以向外找，而且即使在外層已經結束了還能找到，所以在**產生函數**時會在[scope]屬性存放祖先的作用域鏈。注意，是在**產生函數**的時候執行上述動作的。

Scope chain = Activation object + [scope]

****不能理解閉包關鍵是沒有理解ECMAScript模型****。

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

#### 匿名函數引用的name是哪一個變量？
匿名函數的作用域鏈應該有三個pointer，第一個指向匿名函數的變量對象，第二個指向funcA的變量對象，第三個是全局變量對象。根據前述的搜尋順序，因為匿名函數沒有name的函數變量，它的name變量來自funcA（而不是全局變量）。

#### 但是

當`var o = funcA();`執行完後，funcA的變量對象應該就銷毀了，為什麽還可以被訪問到？

關鍵已經提過：****作用域鏈的規則是複製上一層環境的作用域鏈，並將指向當前環境變量對象的指針放到棧頂****。

因此，匿名函數的作用域鏈有三個元素，引用了三個變量對象。返回的匿名函數賦值給變量o，變量o是全局變量，在funcA執行完後不會被銷毀。賦值的時候，匿名函數的作用域鏈已經建立，並用此作用域鏈引用funcA的變量對象，因此，當funcA執行完畢後，其執行環境和作用域鏈確實被銷毀了，但是其變量對象沒有被銷毀，因為匿名函數的作用域鏈對其有引用，無法被垃圾回收機制銷毀。下圖為funcA執行完後的樣子

![](http://images.cnblogs.com/cnblogs_com/leoo2sk/201012/201012201546086372.png)

funcA執行完成後作用域鏈被銷毀，但其變量對象（紅色）因為被匿名函數的作用域鏈引用了，所以沒有銷毀。因此匿名函數可以訪問其成員，當匿名函數完成後，就會連匿名函數的作用域鏈一起被銷毀。

閉包，就是該匿名函數，正式一點說，****閉包就是能訪問另一個函數作用域中變量的函數****。閉包優點是可以訪問另一個函數域中的變量，缺點就是耗記憶體。


## 關於this
****this和作用域沒什麽關係****。在使用閉包時常會誤用this

```
var name = "Global name";

function funcA(){
    var name = "FuncA name";
    return function(){
        alert(this.name);
    };
}

funcA()(); //Global name
```

上面我們在閉包中引用this，本來以為this指向funcA，結果卻是"Global name"，說明this指向全局對象window。因為this指向調用自己的對象，上面的代碼可以寫成`window.funcA()()`，即是window。在全局定義的域中定義的變量和函數都是window的成員。


關於this，可以讀

http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html

和

http://www.zcfy.cc/article/901

和

https://segmentfault.com/a/1190000006076637










