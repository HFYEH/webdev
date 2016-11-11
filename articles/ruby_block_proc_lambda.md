Ruby有三種具有函數功能的東西，即一般的函數，Proc和lambda。

Ruby的函數都是有名字的，如果像JavaScript那樣把函數賦值給另一個變量，只是會執行一次該函數而已。而Proc和lambda就類似匿名函數，是一段程式碼，可以丟給別人執行。

#### 函數

Ruby定義函數很容易
```
def myfun
  puts "myfun"
end

Object.myfun  // myfun
```
以上也表明，如果不給定實例方法所處的位置，此函數就會自動綁定到頂層對象上，成為該對象的方法

#### Proc, lambda

```
myproc = Proc.new { puts "This is a Proc" }
mylamda = lambda { puts "This is a lambda" }
```

附帶一提，下面這個寫法是完全等價的，這是Ruby convention，單行的程式碼就用`{}`，多行就用`do...end`

```
myproc = Proc.new do
  puts "This is a Proc"
end

mylambda = lambda do
  puts "This is a lambda"
end
```

這樣就可以生成一個Proc對象，是一段程式碼，可以之後再執行。

#### 參數

函數，Proc和lambda都可以接收一個block參數，會放在參數的最尾端，有兩種方式，完全等價。函數的的call和yeild分別是顯式和隱式的呼叫block。

```
# 顯式的寫法，如果沒帶block進去會拋出例外
def myfun(*args, &block)
  block.call
end


# 函數中有yield，則呼叫函數時一定要帶block進去，否則一樣會拋出例外
def myfun(*args)
  yield
end

# 可以使用Kernel.block_given?來判斷是否有block傳入，再做相應的措施
def myfun(*args)
  if block_given?
    yield
    puts "Block given"
  else
    puts "No block given"
  end
end
```

第一種寫法可能比較難理解。Ruby中萬物皆對象，唯獨block例外。每次做一個block時，Ruby就會幫忙生成一個Proc對象，指向該block。而把block當參數代入時，其實也是立即被轉成Proc，所以在把Proc當參數引入時，加入`&`符號可以幫我們找回該block。

可以看到以下兩種傳入block的效果是一模一樣的，而代block預設就是轉成Proc

```
def myfun(&block)
  puts block
  yield block
end

myproc = Proc.new { puts "use myproc" }

# 直接代block
myfun { puts "use block" }
# #<Proc:0x000000032330b8@(pry):116># use block

# 代已經定義過的Proc
myfun &myproc
# #<Proc:0x000000032ab770@(pry):114># use myproc
```

函數和Proc, lambda傳遞參數的方式其實很像，只是Block是用`||`

```
myproc = Proc.new { |*args, &block| ... }
```

#### 範例

說了這麽多，還是看看範例吧

```
def myfun(&block)
  block.call("myfun")
end

myproc = Proc.new { |x| puts "#{x} call myproc" }
mylambda = lambda { |x| puts "#{x} call mylambda" }

myfun(&myproc)   # myfun call myproc
myfun(&mylambda) # myfun call mylambda
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

[Programming Ruby](https://pragprog.com/book/ruby4/programming-ruby-1-9-2-0)

[method / block / yield / Proc / lambda 全面解釋](http://railsfun.tw/t/method-block-yield-proc-lambda/110)

[聊聊 Ruby 中的 block, proc 和 lambda](https://ruby-china.org/topics/10414)

