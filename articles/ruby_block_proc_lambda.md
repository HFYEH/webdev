
# Ruby中的block, Proc和Lambda

Ruby有三種具有函數功能的東西，即一般的函數，Proc和lambda。

- Ruby的函數都是有名字的，每次呼叫函數必定會尋找receiver，再決定呼叫哪個物件的同名方法。
- Block包含一段待執行的程式碼，也是Ruby中唯一不是物件的東西。
- Ruby可以將block存成proc物件，再於他處執行。
- Ruby也可以將block存成lambda，與proc有物件有些微不同。lambda表現更像是常見的暱名函數。

## 函數

Ruby定義函數很容易
```
def myfun
  puts "myfun"
end

Object.myfun == myfun  // true
```
以上也表明，如果不給定實例方法所處的位置，此函數就會自動綁定到頂層對象上，成為該對象的方法

## Proc, lambda

Proc或lambda將一段程式碼包裝成物件，供別人使用

```
# 包裝
myproc = proc { puts "This is a Proc" }
mylamda = lambda { puts "This is a lambda" }

# 呼叫
myproc.call(param1, param2, ...)
mylambda.(param1, param2, ...)
```

附帶一提，下面這個寫法是完全等價的，這是Ruby convention，單行的程式碼就用`{}`，多行就用`do...end`

```
myproc = proc do
  puts "This is a Proc"
end

mylambda = lambda do
  puts "This is a lambda"
end
```

Proc和lambda的差異主要有二，一是是否檢查參數，二是return的效果。

### 呼叫參數

```
# 指定要代入參數
myproc = proc {|s| puts "hihi"}
mylambda = lambda {|s| puts "hihi"}

# proc不檢查參數
myproc.call
#=> hihi

# lambda檢查參數
mylambda.call
#=> ArgumentError: wrong number of arguments
```

### Return

Proc的return會跳出調用者的上下文，lambda的return只會跳出lambda自身的上下文

## 使用yeild或call調用的差別

我們可以很簡單的使用yeild調用block

```
def hihi
  puts "Start"
  yield
  puts "End"
end

hihi {puts "yooo"}
#=> Start
#=> yooo
#=> End
```
但這樣我們沒辦法控制調用


```
def hihi(&block)
  puts "Start"
  block.call
  puts "End"
end


```














```
def hihi
  puts "Start"
  yield
  puts "End"
end

# 使用yield時不會產生proc，但預設會將block效果跟proc相同
hihi {puts "yooo"; return}
# => Start
# => yooo
```
lambda的return只會跳出lambda自身的上下文
```
def hihi(x)
  puts "Start"
  x.call
  puts "End"
end

# 使用yield時預設會將block效果跟proc相同
mylamdba = lamdba{puts "yooo"; return}
hihi mylambda
# => Start
# => yooo
```



函數，Proc和lambda都可以接收一個block參數，會放在參數的最尾端，有兩種方式，完全等價。函數的`call`和`yield`分別是顯式和隱式的呼叫block。

```
# 顯式的寫法，如果沒帶block進去會拋出例外
def myfun(*args, &p)
  p.call
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
# #<Proc:0x000000032330b8@(pry):115>
# use block

# 代已經定義過的Proc
myfun &myproc
# #<Proc:0x000000032ab770@(pry):116>
# use myproc

myfun &mylambda
# #<Proc:0x00000003235c00@(pry):117 (lambda)>
#use mylambda
```

函數和Proc, lambda傳遞參數的方式其實很像，只是Block是用`||`

```
myproc = Proc.new { |*args, &p| ... }
```

### 範例

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

### 其他寫法

```
# 下列語法可以產生Proc
Proc.new {|s| ...}
proc {|s| ...}

# 下列語法可以產生lambda
lambda {|s| ...}
-> s {...}
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

### Proc, lambda差異

參考資料中有詳盡的差異說明，這邊就不複製貼上了。我認為比較方便的理解方法是，把p.call當成block，每次執行時，其實就是把block內容整個搬進`p.call`的發生地，因此傳入參數不必詳細檢查，而且return時會直接在`p.call`發生地返回。

而lambda是真正的匿名函數，參數會檢查，而就算lambda return了，也只會回到`p.call`的地方往下繼續執行。

### 參考資料：

[Programming Ruby](https://pragprog.com/book/ruby4/programming-ruby-1-9-2-0)

[method / block / yield / Proc / lambda 全面解釋](http://railsfun.tw/t/method-block-yield-proc-lambda/110)

[聊聊 Ruby 中的 block, proc 和 lambda](https://ruby-china.org/topics/10414)

