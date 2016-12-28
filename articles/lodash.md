# Array

返回第一參數扣除第二參數元素後的陣列，若是物件陣列，會用partial deep方式比較
```
_.difference(arr1, arr2)
_.differenceBy(arr1, arr2, fun)
_.differenceBy(arr_of_obj1, arr_of_obj2, key)
_.differenceWith(arr_of_obj1, arr_of_obj2, _.isEqual)
```

返回第一參數與第二參數的交集元素陣列，若是物件陣列，會用partial deep方式比較
```
_.intersection(arr1, arr2)
_.intersectionBy(arr1, arr2, fun)
_.intersectionBy(arr_of_obj1, arr_of_obj2, key)
_.intersectionWith(arr_of_obj1, arr_of_obj2, _.isEqual)
```

```
_.chunk(arr, 2)    // 拆分陣列為兩兩一組為元素的陣列
_.compact(arr)     // 返回刪除boolean值為false的元素的新陣列
_.concat(arr1, 1, arr2)   // 返回所有參數組成的陣列
```

```
_.drop(arr, 1)         // 返回刪去第一個元素的新陣列
_.dropRight(arr, 1)    // 返回刪去最後一個原素的新陣列
_.take(arr, 1)         // 返回第一個元素
```