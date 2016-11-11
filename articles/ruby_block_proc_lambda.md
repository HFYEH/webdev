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
def myfun(*args, &p)
  block.call
end


# 函數中有yield，則呼叫函數時一定要帶block進去，否則一樣會拋出例外
def myfun(*args)
  yield
end

# 可以使用Kernel.block_given?來判斷是否有block傳入，再做相應的措施
# File.open在有block和沒有block的時候有不同的行為，就是這樣實現的
def myfun(*args)
  if block_given?
    yield
    puts "Block given"
  else
    puts "No block given"
  end
end
```

第一種寫法可能比較難理解。Ruby中萬物皆對象，唯獨block例外。每次做一個block時，Ruby就會幫忙生成一個Proc對象，指向該block。上例中，把block當參數代入時，`p`是被生成的Proc，指向block，而`&p`是block本體，這表明函數是接受block而非Proc。

可以看到以下兩種傳入block的效果是一模一樣的，而代block預設就是轉成Proc

```
def myfun(&p)
  puts p
  yield
end

myproc = Proc.new { puts "use myproc" }
mylambda = lambda { puts "use mylambda" }

# 直接代block
myfun { puts "use block" }
# #<Proc:0x000000032330b8@(pry):116>
# use block

# 代已經定義過的Proc
myfun &myproc
# #<Proc:0x000000032ab770@(pry):114>
# use myproc

myfun &mylambda
# #<Proc:0x00000003235c00@(pry):208 (lambda)>
#use mylambda
```

函數和Proc, lambda傳遞參數的方式其實很像，只是Block是用`||`

```
myproc = Proc.new { |*args, &p| ... }
```

#### 範例

說了這麽多，還是看看範例吧

```
def myfun(&p)
  block.call("myfun")
end

myproc = Proc.new { |x| puts "#{x} call myproc" }
mylambda = lambda { |x| puts "#{x} call mylambda" }

myfun(&myproc)   # myfun call myproc
myfun(&mylambda) # myfun call mylambda
```

若定義方法時，最後一個參數加上`&`，則調用該方法時，Ruby會尋找一個block，並將之轉化為Proc對象，賦值給參數`p`，如此在函數內只要用`p.call`或是`yield`就可以執行該block。

#### 其他寫法

```
# 下列語法可以產生Proc
Proc.new {...}
proc{...}

# 下列語法可以產生lambda
lambda {...}
-> {...}
```

其實也可以把Proc當成變數傳入，這時它就只是一般的參數而已

```
def myfun (proc)
  proc.call
end

p = Proc.new {puts "myproc"}
l = lambda {puts "mylambda" }
myfun (p)
myfun (l)
```

#### Proc, lambda差異

如果函數有接收block，我們會用




### 參考資料：

[Programming Ruby](https://pragprog.com/book/ruby4/programming-ruby-1-9-2-0)

[method / block / yield / Proc / lambda 全面解釋](http://railsfun.tw/t/method-block-yield-proc-lambda/110)

[聊聊 Ruby 中的 block, proc 和 lambda](https://ruby-china.org/topics/10414)

