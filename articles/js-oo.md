這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。


# 概述

JavaScript是用建構函數建成對象的。建構函數形如一般的函數，使用new調用時，裡面的this關鍵字指向一個新對象，由此可以賦予其實例變量和方法。

```
function Person(age){
  this.age = age
}
person = new Person(18)
```

使用`new`關鍵字，這個函數就不是正常調用。它做的事情是

1. 基於構造函數的原型對象創建一個空對象（空對象有一個內部屬性指向原型對象）
2. 將空對象賦值給this
3. 執行建構函數中的語句後返回這個對象

至於什麽是原型對象，之後會說明。

## this 關鍵字


# prototype對象

JavaScript對象的建構是基於原型的，而不是基於類的。基於原型的意思是，對象的生成不再是從一個類實例化而來，而是基於另一個對象生成的。新對象可以取用舊對象（原型對象）的屬性。這樣一個對象疊在另一個對象上的結構，稱為原型鏈。要注意，原型鏈是由對象構成的，而不是建構函數，建構函數只是配帶一個prototype屬性參考到其原型對象的位址而已。

對象可以透過`__proto__`這個內部屬性找到其原型對象。沿著原型鏈最後可以找到頂層對象是`Object.prototype`，而Object.prototype的`__proto__`為`null`，所以所有的對象的`__proto__`，若不是另一個原型對象，就一定是`null`。

***所有函數都會有prototype屬性***，建構函數也不例外。這個`prototype`屬性指向的就是被生成對象的原型對象，當生成新對象時，就會告訴新對象的`__proto__`要指向這個對象。

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

// 生成實例，會自動為對象分配原型對象，透過指定__proto__到建構函數的prototype屬性
var person = new Person("sharefun")

// 檢查對象的建構函數可以用constructor屬性（此屬性為原型對象屬性），後面會介紹
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

可以發現每次生成的新對象，都會生成新的`greet`屬性，但是這個屬性是指向一個函數，函數內容明顯是可以共用的，這時就可以把該函數加到`person`的原型對象上。

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

因為原型對象也是一個對象，也是繼承於另一個原型對象，這形成原型鏈，原型鏈最頂端是`Object.prototype`，剛剛已經說明了。

```
person.__proto__ === Person.prototype
person.__proto__.__proto__ === Object.prototype
person.__proto__.__proto__.__proto__   // null
```

上面提到***建構函數都有一個原型對象（所有函數都有），而原型對象都有一constructor屬性（所有對象都有），此屬性預設指向原型對象所在的建構函數***，也就是兩個互指。因此，所有用建構函數生成的對象，都可以透過調用原型的constructor屬性得到對象的建構函數
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

```
// Object.create(obj)以obj作為原型對象生成新對象
obj = {
  sayHello: function(){
    console.log("Hello");
  }
}

var obj2 = Object.create(obj)
obj2.sayHello();

// 全等於
function O(){}

O.prototype = {}

// 與上一個順序不可顛倒，不然這個屬性就不見了
O.prototype.sayHello = function(){
  console.log("Hello");
}
// 指定原型對象的constructor
O.prototype.constructor = Object

var obj2 = new O()
obj2.sayHello();
```

原型對象的修改和查詢
```
// 原型對象的查詢
Object.getPrototypeOf(person)
person.__proto__ // 只有瀏覽器須部署，而且是內部屬性，應盡量避免使用
person.constructor.prototype  // 手動改原型對象時可能會失效，所以修改原型對象時，要一起改其constructor屬性
```

```
// 前者是對象，後者是建構函數，檢查對象是否在建構函數的原型對象的原型鏈上
person instanceof Person  // true

// 前後都是對象，檢查前者是否是在後者的原型鏈上
person.isPrototypeOf({})  // false
```

```
// 設定對象的原型對象，前者是當前對象，後者是原型對象
Object.setPrototypeOf(person, {})
person instanceOf Person // false
```

接下來說原型繼承。**JavaScript的原型繼承的本質：將建構函數的原型對象指向由另一個建構函數創建的實例。**

```
// 建構函數預設的原型對象就是Object()的實例
function Person(){}
Person.prototype  // Object

function Girl(){}
Girl.prototype = new Person()
// 現在Girl建構函數繼承了Person的建構函數

// 所以Girl實例的prototype指向的是Person實例
var girl = new Girl()
girl.prototype  // Person
girl.constructor // function Person(){}
```

可以看到連constructor也被改了。這是因為在為Girl設置prototype時，是將原型對象指向Person的實例，這個實例中的constructor屬性自然是指向Person建構函數。girl對象繼承的的原型對象從Girl.prototype變成Person.prototype，所以去找constructor時會找到Person.prototype。如果想要讓constructor看起來是正確的，可以改寫一new Person()產生的原型對象。

```
Girl.prototype = new Person();
Girl.prototype.constructor = Girl;   // Girl.prototype.constructor就是指向上一行的new Person()
```


## Object 對象與繼承



## 物件導向的編程模式

