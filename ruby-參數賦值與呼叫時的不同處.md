## self

通常，類中的方法如不標明，會使用一隱式的self作為接收者。所以在下列兩個方法中，都可以取得變量`m`的值。然而，如果是要賦值給變量`m`，則必須顯式的調用，否則會被認為是局部變量，而無法賦值。
 
```
class C
  attr_accessor :m
  def with_self(value)
    puts m    # self.m
    self.m = value
  end

  def wo_self(value)
    puts m    # self.m
    m = value
  end
end

c = C.new
c.with_self("Hello from with_self")
c.m
# => "Hello from with_self"


c.wo_self("Hello from wo_self")
c.m
# => "Hello from with_self"

```



## *

`*`在等號左邊時，用於將該變量轉為array，出現在等號右邊時，用於將array物件展開

```
# 賦值時，若最後一個左值有*，表明該變量把所有多餘的右值存入
a, *b = 1,2,3,4
a  # => 1
b  # => [2,3,4]

# 如果最後一個右值有*，表明會將該變量展開
a, *b = 1, *[2,3,4]
```


## &

`&`在方法定義中，是將block轉為proc對象，而在調用方法時，是將proc對象還原為block

```
# 接收一個block，&會將之轉為proc對象，並存入p中，可以在方法中調用p的call方法
def foo(&p)
  p.call
end

foo { puts "Hello" }

# 在方法中，使用&可以將proc對象轉回block，而instance_eval後面接一個block，可以以當前的上下文來執行該block
def bar(&p)
  instance_eval &p
end

bar { puts "Hello" }
```


