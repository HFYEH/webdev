<!-- TOC -->

- [Ruby中的block, Proc和Lambda](#ruby中的block-proc和lambda)
    - [函數](#函數)
    - [Proc, lambda](#proc-lambda)
        - [呼叫參數](#呼叫參數)
        - [Return](#return)
        - [使用yield或call調用的差別](#使用yield或call調用的差別)
        - [其他寫法](#其他寫法)
    - [參考資料：](#參考資料)

<!-- /TOC -->
# Ruby中的block, Proc和Lambda

Ruby有三種具有函數功能的東西，即一般的函數，Proc和lambda。

- Ruby的函數都是有名字的，每次呼叫函數必定會尋找receiver，再決定呼叫哪個物件的同名方法。
- Block包含一段待執行的程式碼，也是Ruby中唯一不是物件的東西。Block本身被設計成不能access的，只能在被包裝成物件後才能被access。
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

### 使用yield或call調用的差別

我們可以很簡單的使用yield關鍵字（不是方法）調用block

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
但這樣我們沒辦法access block。要access，可以包裝block成proc
```
def hihi(&p)
  puts "Start"
  p.call  # p是一個proc物件，因為&p會將block轉成proc存入p
  puts "End"
end

hihi {puts "yooo"}
#=> Start
#=> yooo
#=> End
```
雖然這樣造成一些效能損耗，但是你可以決定要在哪一個execution context執行該proc，這在建立DSL時特別有用。


```
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

可以看到以下兩種傳入block的效果是一模一樣的，而代block預設就是轉成Proc

```
def myfun(&p)
  puts p
  yield
end

myproc = proc { puts "use myproc" }
mylambda = lambda { puts "use mylambda" }

# 直接代block
myfun { puts "use block" }
#=> #<Proc:0x000000032330b8@(pry):115>
#=> use block

# 代已經定義過的Proc
myfun &myproc
#=> #<Proc:0x000000032ab770@(pry):116>
#=> use myproc

myfun &mylambda
#=> #<Proc:0x00000003235c00@(pry):117 (lambda)>
#=> use mylambda
```

### 其他寫法

```
# 下列語法可以產生Proc
Proc.new {|s| ...}
proc {|s| ...}

# 下列語法可以產生lambda
lambda {|s| ...}
-> s {...}
```

## 參考資料：

[Programming Ruby](https://pragprog.com/book/ruby4/programming-ruby-1-9-2-0)

[method / block / yield / Proc / lambda 全面解釋](http://railsfun.tw/t/method-block-yield-proc-lambda/110)

[聊聊 Ruby 中的 block, proc 和 lambda](https://ruby-china.org/topics/10414)

