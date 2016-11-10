函數都可以接收一個block參數，有兩種方式


若定義方法時，最後一個參數加上&，則調用該方法時，Ruby會尋找一個block，並將之轉化為Proc對象，賦值給參數，如此可以像處理其他變量一樣處理該參數。
```
def method_1(*args, &block)
  block.call
end
```

採自Programming Ruby的例子
```
def n_times(thing)
  return lambda { |n| thing*n }
end

p = n_times("Hello ")    # 產生closure，記住了定義block時的上下文，即self，作用域內的方法，常量和變量

p.call(3)    # Hello Hello Hello，生成環境雖已消失，但是block仍可以使用原始作用域中的訊息
```



```
def method_2(*args)
 yield
end

```