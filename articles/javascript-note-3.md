這裡記錄一些從阮一峰的[JavaScript參考教程](http://javascript.ruanyifeng.com/)和 JavaScript: The Good Part 中得到的知識，做記憶補強和方便回憶之用。

## valueOf(), toString()

valueOf()返回對象的原始類型值，toString()將對象轉為字串。

## 強制轉換

#### Number()

###### 原始類型

```
Number(324) // 324
Number('324') // 324
Number('324abc') // NaN
Number('') // 0
Number(true) // 1 
Number(false) // 0
Number(undefined) // NaN 
Number(null) // 0
```

###### 複合類型

簡單規則，Number操作在對象上會返回NaN
[詳見](http://javascript.ruanyifeng.com/grammar/conversion.html#toc1)

#### String()

###### 原始類型

```
String(123) // "123"
String('abc') // "abc"
String(true) // "true"
String(undefined) // "undefined"
String(null) // "null"
```

###### 複合類型

[詳見](http://javascript.ruanyifeng.com/grammar/conversion.html#toc2)

#### Boolean()

###### 原始類型

除下列為false之外其餘皆為true
```
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
```

## 自動轉換

自動轉換是基於上述的強制轉換，有很大的不確定性，且除錯不易，建議盡量使用強制轉換。