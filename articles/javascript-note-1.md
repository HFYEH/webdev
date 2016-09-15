這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## 數據類型

JavaScript的數據類型共有六種（不包含ES6的symbol），即string, number, boolean, undefined, null, object。前三種屬於原始的類型，但也可以用對象方式調用。前三種的值可以被改變，object則是引用，無法被直接改變。

其中object又分三種，普通的object, array 和 function。

把函數當成一種數據類型，使其可以像數據一樣被傳遞和賦值，體現了它作為「函數式語言」的特質。

利用`typeof`可能得到的結果有：

1. 原始類型的"string", "number", "boolean"
2. 函數類的"function"
3. undefined 會返回 "undefined"   (typeof 可檢查沒有聲明的變量而不報錯）
4. "object"，包含一般對象，array和null

Array是一種特殊的object，使用typeof都會得到"object"，如要區分array和object，可用`instanceof`。

null和undefined的歷史有完整的說明。使用上，***null代表空的值，而undefined代表未定義***。

要檢查變量是否存在，使用`(typeof varaiable === "undefined)`

邏輯運算符（&&, ||, ===, !==, >=, ... ）會返回boolean，下列六個值會被轉成false

1. undefined
2. null
3. false
4. 0
5. NaN
6. "", ''

`{}`和`[]`都是true，須注意。


#### Number

* `+0`和`-0`兩個是等價的，唯一差別是以其作為分母結果為`Infinity`和`-Infinity`
* `NaN` 是一種number，代表一種特殊數值(0/0會得到NaN)，不等於包括自己在內的任何值，可以用isNaN(some_number)判斷是否為NaN，因為isNaN只對數值有效，裡面放數值外的東西會被轉型為NaN，須注意
* 因為NaN是唯一不等於自己的東西，可以用some_var === some_var來判斷（最保險且直接）
* Infinity大於除了NaN以外的所有數值，-Inifinity小於除了NaN以外的所有數值
* isFinite()對Infinity, -Infinity, NaN都會得到false

數值相關的全局方法

* `parseInt()`回傳字串轉成的***十進位整數***或NaN
* `parseInt('0x10')`返回16，若字串第一位為非數字，則返回NaN
* `parseInt('100', 2)`第二個參數代表第一個參數的進制，此例中，返回值為8
* `parseFloat`回傳字串的轉成的***浮點數***

#### String

* 字串是唯讀的，欲對字串內字元操作，必須先轉成array
* 字串的length屬性也是唯讀的
* JavaScript使用Unicode，且可以直接使用unicode，比如'\u0049'
* JavaScript字串長度固定為兩個字節，即16位元。32位元的unicode會被當成兩個字節

Base64

* Base64轉碼是為了不出現特殊字元
* `btoa()`字串或二進位轉成Base64
* `atob()`Base64轉為原來的編碼
* 僅能用於ACSII

#### Object

建立對象
```
// 以下三種寫法是等價的
var o = {}                    // 簡潔
var o = new Object()          // 顯示使用constructor的意圖
var o = Object.create(null)   // 須要對象繼承時使用
```

* 對象是無序的key-value的集合
* key必定是字串，大部份時候可省略引號（不合法的key就要加引號），就算是數值也會被轉成字串
* 由於function可以被當作值傳遞進object，當某個key對應的是一般變數時，我們稱其為object的屬性，當某個key對應到的是一個函數時，我們稱其為方法

傳值引用

```
var o1 = {}
var o2 = o1
# 由於對象的引用是傳址，改變o1的內容會同時反映在o2上，對原始型態的話則是傳值引用
```

一些對象相關的方法

```
'a' in window    # 檢查屬性是否被聲名的正確寫法，但是無法分辨該屬性是否是繼承來的
Object.keys(o)   # Object.keys()可檢查物件所有key
delete o.p       # 刪除p的o屬性，僅能刪除自有屬性，刪除成功返回true，失敗返回false，返回true的狀況只能保證o.p為undefined，不能保證其值原本存在，使用var聲明的變量也預設無法被刪除
for ... in       # 會遍歷所有可遍歷的屬性，包含繼承來的屬性
for              # 只會取出自己的屬性
```

#### Array

* Array是一種特殊對象，其key是由按次序排列的整數字串
* 可以使用`[]`取值，不能像一般對象使用`.`取值，因為數值不能作為標示符

###### length

* length屬性可以返回array成員數量，length為動態的，其值為***key中最大整數+1***
* length屬性可以更改，array會自動變成該長度，多則刪除，少則補上undefined
* length屬性是用32位元整數保存的，亦即其最大值為4294967295

```
var a = [1,2,3,4,5]
a.length        // 5
a["100"] = 3
a.length        // 101

a.length = 2
a               // [1,2]
a.length = 10
a               // [1,2, undefined*8]
a.length = 0
a               // []
```

* Array既是對象，就可以任意新增key，只不過array.length還是依照上述原則返回值

```
a = []
a["p"] = 123
a.length    // 0
```

###### Array-like object

* Array-like的對象有一個特徵，即具有length屬性。但此length屬性不是動態值。
* 典型的array-like object有函數的arguments，DOM元素集和string。

`slice`可以將array-like object轉換成array

```
var arr = Array.prototype.slice.call(arraylike)
```

###### 遍歷
Array.prototype.forEach.call和for皆可遍歷array和array-like object。

for...in也可以遍歷array，但是會把非數字的key也遍歷到。不建議使用。

###### in
`in`也可以用來檢查key是否存在，數字會被轉為字串

```
var arr = []
arr[100] = 'a';
100 in arr // true
1 in arr   // false
```

###### 空位

```
var a = [1,,1];
a.length // 3 （中間會產生一個空位）
a[1]     // undefined （空位是可以被讀取的，返回值是undefined）

var b = [1,2,3,];
b.length // 3 （最後一個逗號不影響length）
delete b[2]
b        // [1,2,undefined x 1]  （使用delete會產生一個空位）
b.length // 3 （使用delete不會影響length屬性，使用上要注意）

// undefined和空位是不同的，空位表示沒有此元素，undefined表示有此元素，值為undefined
var c = [1,undefined, undefined, , , 3]
c    // [1,undefined, undefined, undefined x 2, 3]
// 因此使用forEach或for...in和Object.keys，空位都會被跳過
```

###### 原型：

每個對象都連接到一個原型對象，並從中繼承屬性。

所有通過literal創建的對象都連到Object.prototype這個JavaScript中標準對象。

原型連接在更新時不起作用，對某個對象做改變，不會影響該對象原型。

原型連接只有在檢索才會被找到，我們在對象中找屬性，一旦找不到，就會依原型連接往上找，直到找到或追到Object.prototype為止，若都沒有該屬性則回傳undefined。

原型是動態的，為原型新增一個屬性後，所有基於該原型創建的對象皆可見該屬性。

for in 可以遍歷對象所有的屬性名，包括原型中的屬性，如果分辨是否是對象自己的屬性，可以用hasOwnProperty()以及使用typeof過濾函數。

for 則可以只取對象自身的屬性，不會取出原型鏈中的屬性，而且與for in不同，它會照順序。

delete 用來刪除屬性，它不會觸及原型鏈中的對象，但刪除與原型同key的屬性，會讓原型的屬性曝露出來。


#### 函數

JavaScript作為一門函數編程語言，將函數看成一種值，與其他數據類型地位相同，因此可以將函數作為返回值或參數傳入其他函數，或視賦值給變量和對象的屬性。

function declaration

```
function someFun(){}
```

function expression (函數表達式)
```
var someFun = function hi(){
    console.log(typeof hi);
};
hi // UnreferenceError: hi is not defined

someFun()  // "function"
```
使用函數表達式時，函數名僅在函數內部可見。函數聲明不用加分號，但函數表達式因為是表達式，應該加分號。


###### 函數的屬性和方法

1. name屬性：返回函數名
2. length屬性：函數***定義時***的參數個數
3. toString()方法：返回該函數的原始碼

###### 函數作用域

作用域有兩種，一是全局作用域，一是函數作用域。全局變量在全局和函數內可見，函數內變量只有函數內可以讀取。但如果全局和函數有一個同名變量，則在全局時會讀全局的值，在函數內，該變量會被函數變量覆蓋，所以會讀函數內定義的值。要在函數內使用函數變量，要加`var`，否則函數會認為是全局變量。

函數因為地位與其他數據類型相同，所以也存在變量提升，函數內部也有變量提升。

與其他變量相同，函數存在的作用域，就是其***聲明時***所在的作用域，而不是其調用時的作用域。而當函數B聲明在某一函數A內時，函數B作用域就會綁定到函數A的函數作用域。

###### 參數
