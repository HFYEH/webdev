函數都可以接收一個block參數，有兩種方式

```
def method_1(*args, &block)
  block.call
end

def method_2(*args)
 yield
end




```