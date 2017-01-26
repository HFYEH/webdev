這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

# prototype對象

JavaScript對象的建構是基於原型的，而不是基於類的。基於原型的意思是，對象的生成不再是從一個類實例化而來，而是從另一個對象生成。JavaScript因為沒有類，所以用建構函數生成對象。

在繼續說明前，要先知道，所有對象都繼承自另一個對象，除了null之外。原型對象上的屬性和方法，都會被衍生的對象共享。


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

// 查看此對象的原型對象
person.__proto__     // Object {}

// 這個原型對象怎麽來的呢？其實就是建構函數的prototype屬性指向的對象
Person.prototype === person.__proto__  // true

// 呼叫對象自身方法，不過一般不會這樣用，而是把該方法寫在原型對象中
person.greet() // "Hi, sharefun"
```

可以發現每次生成的新對象，都會帶有`greet`屬性，但是這個屬性是指向一個函數，函數內容明顯是可以共用的，這時就可以把該函數加到`person`的***constructor的原型對象上***。