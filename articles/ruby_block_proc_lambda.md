Ruby有三種具有函數功能的東西，即一般的函數，Proc和lambda。

Ruby的函數都是有名字的，如果像JavaScript那樣把函數賦值給另一個變量，只是會執行一次該函數而已。而Proc和lambda就類似匿名函數，是一段程式碼，可以丟給別人執行。

#### 函數

Ruby定義函數很容易
```
def myfun
  puts "myfun"
end

Object.ooo  // myfun
```
以上也表明，如果不給定實例方法所處的位置，此函數就會自動綁定到頂層對象上，成為該對象的方法

#### Proc, lambda

```
func1 = Proc.new { puts "This is a Proc" }
func2 = lambda { puts "This is a lambda" }
```

附帶一提，下面這個寫法是完全等價的，這是Ruby convention，單行的程式碼就用`{}`，多行就用`do...end`

```
func1 = Proc.new do
  puts "This is a Proc"
end

func2 = lambda do
  puts "This is a lambda"
end
```


這樣就可以生成一個Proc對象，是一段程式碼，可以記住

函數都可以接收一個block參數，有兩種方式，完全等價

```
def method_2(*args)
  yield
end

def method_1(*args, &block)
  block.call
end
```

若定義方法時，最後一個參數加上&，則調用該方法時，Ruby會尋找一個block，並將之轉化為Proc對象，賦值給參數，如此可以像處理其他變量一樣處理該參數。

採自Programming Ruby的例子
```
def n_times(thing)
  return lambda { |n| thing*n }
end

p = n_times("Hello ")    # 產生closure，記住了定義block時的上下文，即self，作用域內的方法，常量和變量

p.call(3)    # Hello Hello Hello，生成環境雖已消失，但是block仍可以使用原始作用域中的訊息
```


### 參考資料：

[method / block / yield / Proc / lambda 全面解釋](http://railsfun.tw/t/method-block-yield-proc-lambda/110)


