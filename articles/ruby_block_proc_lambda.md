函數都可以接收一個block參數，有兩種方式


若定義方法時，最後一個參數加上&，則調用該方法時，Ruby會尋找一個block，並將之轉化為Proc對象，賦值給參數，如此可以像處理其他變量一樣處理該參數。
```

def method_1(*args, &block)
  block.call
end



def method_2(*args)
 yield
end




```