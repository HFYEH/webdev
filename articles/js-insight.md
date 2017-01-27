這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

# prototype對象

JavaScript對象的建構是基於原型的，而不是基於類的。基於原型的意思是，對象的生成不再是從一個類實例化而來，而是從另一個對象生成。JavaScript因為沒有類，所以用建構函數生成對象。

在繼續說明前，要先知道，所有對象都繼承自另一個對象，除了null之外。原型對象上的屬性和方法，都會被衍生的對象共享。

又，***所有函數都會有prototype屬性***，建構函數也不例外。這個prototype屬性指向的就是原型對象，用此建構函數生成的對象都繼承於這個原型對象。

```javascript
// 建構函數
function Person(name){
  // 將會生成對象屬性
  this.name = name;
  
  // 將會生成對象自有方法
  this.greet = function(){
    console.log("Hi, " + this.name);
  }
}

// 生成實例，會自動為對象分配原型對象
var person = new Person("sharefun")

// 檢查對象的建構函數可以用constructor屬性（此屬性為原生對象屬性），後面會介紹
person.constructor  // Person(){...}

// 或可用 instanceof 檢查
person instanceof Person // true

// 查看此對象的原型對象
person.__proto__     // Object {constructor: ..., __proto__: ...}

// 這個原型對象怎麽來的呢？其實就是建構函數的prototype屬性指向的對象
Person.prototype === person.__proto__  // true

// 呼叫對象自身方法，不過一般不會這樣用，而是把該方法寫在原型對象中
person.greet() // "Hi, sharefun"
```

可以發現每次生成的新對象，都會生成新的`greet`屬性，但是這個屬性是指向一個函數，函數內容明顯是可以共用的，這時就可以把該函數加到`person`的***constructor的原型對象上***（`person`自己是沒有prototype的）。改寫該方法到原型對象上。

```
function Person(name){
  this.name = name;
}

Person.prototype.greet = function(){
  console.log("Hi, " + this.name);
}

var person = new Person("sharefun")

// 現在這個原型對象就帶一個新的屬性`greet`了
person.__proto__  // Object {constructor: ..., greet: ..., __proto__: ...}

```

原型對象也是一個對象，也是繼承於另一個原型對象，這形成原型鏈，原型鏈最頂端是`Object.prototype`

```
person.__proto__ === Person.prototype
person.__proto__.__proto__ === Object.prototype
person.__proto__.__proto__.__proto__   // null
```

上面提到***建構函數都有一個原型對象（所有函數都有），而原型對象都有一constructor屬性（所有對象都有），此屬性預設指向原型對象所在的建構函數***。因此，所有用建構函數生成的對象，都可以透過調用原型的constructor屬性得到對象的建構函數
```
Person.prototype.constructor === Person  // true

person.constructor === Person  // true
```

```
// 檢查建構函數Person的原型對象，是否在person的原型鏈上
person instanceof Person  // true

// 原型鏈上可能有多個原型對象，所以像下列的也會回傳true
person instanceof Object  // true
```


