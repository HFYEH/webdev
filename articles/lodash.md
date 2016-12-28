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

