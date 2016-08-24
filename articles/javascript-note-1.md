# JavaScript: The Good Part

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

如要區分array和object，可用`instanceof`。

null和undefined的歷史有完整的說明。使用上，***null代表空的值，而undefined代表未定義***。

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

```
var o1 = {}
var o2 = o1
# 由於對象的引用是傳址，改變o1的內容會同時反映在o2上，對原始型態的話則是傳值引用
```





for in回取出原型的屬性，並且沒有固定的順序

for只會取出自己的屬性，且有出現的順序

# 第三章　對象

JavaScript的簡單類型有number, string, boolean, null, undefined，它們是****不可變的****。

JavaScript對象是可變的keyed collections。對象是屬性的容器，屬性為key / value對，key是任意字串，value是undefined之外的任意值。

對象透過引用傳遞，永遠不會被拷貝。

##### 原型：

每個對象都連接到一個原型對象，並從中繼承屬性。

所有通過literal創建的對象都連到Object.prototype這個JavaScript中標準對象。

原型連接在更新時不起作用，對某個對象做改變，不會影響該對象原型。

原型連接只有在檢索才會被找到，我們在對象中找屬性，一旦找不到，就會依原型連接往上找，直到找到或追到Object.prototype為止，若都沒有該屬性則回傳undefined。

原型是動態的，為原型新增一個屬性後，所有基於該原型創建的對象皆可見該屬性。

for in 可以遍歷對象所有的屬性名，包括原型中的屬性，如果分辨是否是對象自己的屬性，可以用hasOwnProperty()以及使用typeof過濾函數。

for 則可以只取對象自身的屬性，不會取出原型鏈中的屬性，而且與for in不同，它會照順序。

deleteｍ用來刪除屬性，它不會觸及原型鏈中的對象，但刪除與原型同key的屬性，會讓原型的屬性曝露出來。

# 第四章　函數
