這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## Object

所有的JavaScript對象都繼承自Object對象。Object對象本身也是一個構造函數，可以透過它生成對象，也可以用來包裝原始類型為對象。

```javascript
var o = new Object()  // 全等於 o = {}

// 如果傳入的參數是對象，就返回該對象
var o1 = {a: 1};
var o2 = new Object(o1);
o1 === o2 // true
// 如果傳入的參數是原始類型，就返回其值對應的包裝對象
new Object(123) instanceof Number // true
```
#### 部署方法

###### 部署在Object對象 - 構造函數方法

方法存在Object中，如果其它對象想要使用，必須呼叫Object的方法。

```javascript
Object.print = function(o){console.log(o)}
var o = new Object();
Object.print(o);   // Object
```

###### 部署在Object.prototype - 實例方法

**所有構造函數都有一個prototype屬性，指向一原型對象。為原型定義的所有屬性和方法，將被所有實例共享。**

```javascript
Object.prototype.print = function(){console.log(this)};
var o = new Object();
o.print()      // Object
```

#### Object自身的方法

```javascript
Object.keys()   // 返回可枚舉的屬性
Object.getOwnPropertyNames()  // 返回所有屬性（如Array的length屬性不可枚舉，在此會被列出
hasOwnProperty()     // 判斷某属性是否為當前對象自身的屬性
```

#### Object對象的實例方法

```javascript
Object.prototype.valueOf()  // 預設返回對象自身
Object.prototype.toString()  // 返回對象的字串型式。比如Date()會被改寫成日期時間的字串，是因為覆蓋了Object.prototype.toSting
var a = {b:1}
a.toSting()   // [object Object] 第二個表示該對象的構造函數，可用於判斷數據類型。

// 因為返回的是構造函數，因此可以比typeof更精確的回傳變量類型，常用的只有NaN會返回Object而已，不過要用Object.prototype.toString.call，因為實例方法已經被覆蓋了

// 以下總結Object兩種部署方法與實例間的關係
var o1 = new Object();                // 建立o1
var o2 = new Object();                // 建立o2
o1.toString();                        // [object Object]  從Object.prototypr繼承來的toString會返回建構函數字串
o1.toString = function(){
  return "o1's toString instance method"
}                                     // 覆寫實例方法
o1.toString();                        // "o1's toString instance method"
o2.toString();                        // [object Object]  不會覆蓋到其他實例
Object.prototype.toString.call(o1)    // [object Object]  使用call可以呼叫到被覆概的方法
Object.prototype.toString = function(){
　　"Object constructor method"
}                                     // 覆蓋建構函數方法
o1.toString();                        // "o1's toString instance method"   因為被覆蓋了
o2.toString();                        //  "Object constructor method"    o2使用繼承來的建構函數方法
```

JavaScript是採用原型繼承，所有實例都會繼承其對象原型中的方法和屬性。其他基本的原型，如Date.prototype, Number.prototype等，都繼承於Object.prototype。

當使用對象方法或呼叫一個對象的屬性時，會沿著其原型繼承鏈一層一層往上找，直到找到為止。

## Array

因為建構函數的不確定性，建議總是用literal方式產生Array變量。

如果要初始化特定長度的array，可以使用new Array(n)或Array(n)

使用Array.isArray()檢查變量是否為Array。

```javascript
var arr = [];    // 建構array object
Array.isArray(arr)   // true
```

#### 實例方法

###### 與Object相同的
一樣有valueOf(), toString()

###### push

添加變量x到最後一個元素，***會改變原array***

`arr.push(x)`

合併兩array

```javascript
a = [1,2,3]
b = [4,5,6]
Array.prototype.push.apply(a,b)
a   // [1,2,3,4,5,6]
```

###### pop

刪除最後一個元素，並返回該元素，***會改變原array***

```javascript
var arr = [1,2,3]
arr.pop()  // 3
arr        // [1,2]
```

###### shift

與pop類似，只不過刪除的是第一個元素，並返回。***會改變原array***

###### unshift

與push類似，只不過是增加為第一個元素，***會改變原array***

###### reverse

顛倒整個array的順序，***會改變原array***

###### join
將array合併為字串返回。
```javascript
var a = [1,2,3]
a.join()  // "1,2,3"
```
Array.prototype.join可以用於array-like對象
```javascript
Array.prototype.join.call('hello','-')  // "h-e-l-l-o"
```

###### concat
array合併，接收參數接到原array之後，並返回，原array不變。規則是所有參數會打破一層array，其中所有元素會依序接上，如果某個參數是原始數值，則直接接上。如果有兩層，第二層會以引用方式作為一個元素接上。

```javascript
var a = [1,2,3]
var b = [4,5,6]
a.concat(b)  // [1,2,3,4,5,6]
a.concat(4,5,6) // 同上
a.concat(4,[5,6]) // 同上

[0,1].concat([2],[3,4],[5,[6,7]],8)   // [0, 1, 2, 3, 4, 5, Array[2], 8]
```javascript
注意，只要原array中包含對象，返回的array包函對該對象的引用而非硬拷貝。

###### slice

從array切出另一個array並返回，原array不變。代入參數為起點和終點index，若只代入一個參數，則返回該位置元素一直到最後為止。

這通常拿來複製array，或將array-like對象轉為array

```javascript
// 複製array
arr1 = [1,2,3]
arr2 = arr1.slice(0)  // arr2是新的array

Array.prototype.slice.call({0: 'a', 1: 'b', length: 2})  // ['a','b']
```

###### splice

從array刪除一些元素，並在刪除的位置加入新元素，返回值是被刪除的元素。***會改變原array***

```javascript
var arr = [0,1,2,3,4,5,6,7]
arr.splice(1,5,'x','y','z')  # [1, 2, 3, 4, 5]從index=1開始刪除5個元素arr  # [0, "x", "y", "z", 6, 7]

arr.splice(1,'w')
arr  # [0, "w", "x", "y", "z", 6, 7]
```

###### sort

按照字碼改變排序，數字會先被轉成字串。***會改變原array***

可以使用自定的排序方式

```javascript
var arr = [100,111,102]
arr.sort(function(a,b){return a-b})
```

###### map

對所有元素調用一個函數，根據函數返回值組成一個新array。

map第二個參數可以代入原array，這樣就可以在map的第一個函數參數內使用this關鍵字
```javascript
var arr = ['x','y','z']
arr.map(function(e, index){return arr[index] == e}, arr)
// [true, true, true]
```

map不會遍歷到array的空位。

###### forEach

同map，用於不返回值的時候。不像for loop，它總是會遍歷完所有元素。


###### filter

參數為一函數，會返回true或false，該函數會遍歷array所有元素，最後將返回true的元素們重組為一新array，不改變原array。

函數參數型式同map和forEach，可代index為第二參數，而filter本身亦可以代入第二參數，將this綁定到目前處理的array上。


###### some, every

some: 參數為一函數，只要其中一個返回true，就返回true，反之則為false

every: 參數為一函數，全部都返回true，才返回true，反之則為false


###### reduce, reduceRight

reduce就是ruby的inject，函數參數的第一個參數為累積值，第二個參數為當前元素，第三個參數是index

reduce的第二個參數可以對累積變量指定初始值。

reduceRight只是從尾巴開始逆向迭代而已。

```javascript
[1,2,3,4,5].reduce(function(sum, x){return x+y})
// 15
```


###### indexOf, lastIndexOf

indexOf在array中尋找等於第一參數的第一個元素，返回其位置，lastIndexOf則是找最後一個等於的元素。

第二個參數表示從該位置開始往後找。

