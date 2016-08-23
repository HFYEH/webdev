# JavaScript: The Good Part

這裡記錄一些從 JavaScript: The Good Part 的和阮一峰的JavaScript參考教程中得到的知識，做記憶補強和方便回憶之用。

###### 數據類型

JavaScript的數據類型共有六種（不包含ES6的symbol），即string, number, boolean, undefined, null, object。前三種屬於原始的類型，但也可以用對象方式調用。

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

