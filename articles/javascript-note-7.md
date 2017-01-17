這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## Math

屬性
```
Math.E：常數e。
Math.LN2：2的自然對數。
Math.LN10：10的自然對數。
Math.LOG2E：以2為底的e的對數。
Math.LOG10E：以10為底的e的對數。
Math.PI：常數Pi。
Math.SQRT1_2：0.5的平方根。
Math.SQRT2：2的平方根。
```
方法
```
Math.abs()：絕對值
Math.ceil()：向上取整
Math.floor()：向下取整
Math.max()：最大值
Math.min()：最小值
Math.pow()：指數運算
Math.sqrt()：平方根
Math.log()：自然對數
Math.exp()：e的指數
Math.round()：四捨五入
Math.random()：[0-1)的隨機數
```

## Date

範圍：基準時間1970年1月1日00:00:00前後一億日

```
Date()            // 直接調用對象，返回當地時間的字串，參數無效
// "Thu Oct 20 2016 11:54:20 GMT+0800 (CST)"

new Date()        // 用建構函數會返回一個當地時間的實例對象，參數為時間
var now = new Date();

// 建構函數的參數有三種：
new Date(0)               // 基準點起算的毫秒數
// Thu Jan 01 1970 08:00:00 GMT+0800 (CST)  返回基準時間

new Date("Jan 01, 1970")  // 日期字串
// 日期字串完整格式為“month day, year hours:minutes:seconds”，省略值會被設為0，字串會先經過Date.parse()，所以只要可以被解析，就可以當作參數輸入。
// 注意：使用YYYY-MM-DD可能會得到格林威治國際標準時的時區，可以得到當地的時間，如果採用其他格式，如YYYY/MM/DD，則會假定使用者處於本地時區。

new Date(1970, 0, 1, 0, 0, 0, 0)   // 代入年月日時分毫秒為參數
// Sun Feb 01 1970 00:00:00 GMT+0800 (CST)
// 注意：月從0起算，日從1起算
```

#### 運算

```
var d1 = new Date(2000, 2, 1);
var d2 = new Date(2000, 3, 1);
d2 - d1 // 2678400000  相減可得毫秒差
```

#### Date對象靜態方法
```
Date.now()   // 基準時間到當前時間的毫秒數
Date.parse() // 將字串解析並返回距基準時間的毫秒數
Date.UTC(1970,1,1)  // 使用法與new Date()第三種一樣


// 假定我在GMT+8
var d1 = new Date(2016,10,10,8,0)  // 表示輸入是當地時間
var d2 = Date.UTC(2016,10,10,0,0)  // 表示輸入是本地時間
Date.parse(d1) == d2
// true
```

#### Date對象實例方法
```
toString()      // 讀取Date對象的預設的方法
toUTCString()   // 返回UTC時間的字串
toJSON()        // 符合JSON格式的ISO格式時間字串
toDateString()
toTimeString()
toLocaleDateString()
toLocaleTimeString()

getDate()
getDay()        // 返回星期幾，0是星期日，隨後遞增
getYear()
getFullYear()
getMonth()     // 0表示一月
getHours()
getMinutes()
getSeconds()

etc etc...
```

## RegExp

