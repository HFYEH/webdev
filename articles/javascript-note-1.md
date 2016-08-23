# JavaScript: The Good Part

這裡是一些閱讀JavaScript: The Good Part的筆記。內容穿插從阮一峰的JavaScript參考教程中得到的知識，將以引用的方式表現，如下：

>此段引自JavaScript參考教程。

# 第二章　語法

如果變量是Array或null，結果是`object`，這是錯的。

> JavaScript的數據類型共有六種（不包含ES6的symbol），即string, number, boolean, undefined, null, object。而其中object又分三種，普通的object, array 和 function。


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

