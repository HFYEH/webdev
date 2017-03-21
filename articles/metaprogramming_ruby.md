此系列為Metaprogramming Ruby第二版的筆記，記錄於此作為複習之用。

# Metaprogramming Ruby

此系列為Metaprogramming Ruby第二版的筆記，記錄於此作為複習之用。

## The Object Model

### Open Class

Ruby透過內省機制，在執行階段可以存取class內部資訊。亦即class是可以被打開的。

```
3.times do
  class C
    puts "Hello"
  end
end
```

每次執行時，都打開了這個class C，並且執行了`puts "Hello"`。第一次，C沒有定義，所以Ruby會定義，而內容會被執行，第二次和第三次，因為已經定義過了，Ruby只是打開它並執行裡面的語句而已。

class關鍵字比較像是作用域操作符，其任務是把你帶到class的上下文中，讓你可以在裡面定義方法。

改變一個現有的方法比較有風險，較安全的作法是新增一個方法。

### Inside the Object Model

`object`本身只包含實例變量，其方法作為實例方法存在於其class中。

`SomeClass`在Ruby中也是一種object，這種object預設會有幾種實例方法，定義在Class裡。所有的SomeClass都是Class的實例。

```
Class.instance_methods(false)  #=> [:allocate, :new, :superclass]
```

所以當我們定義SomeClass，都會具備這三個方法。第一個書中說幾乎不會用到，new是產生新實例的時候用的，superclass是引用其父輩class。（所以SomeClass這些object間有繼承關係，但一般object沒有）

`Module`是Class的父輩，Class只是一種Module，多了可以實例化object的功能。

Class和Module很接近，但一般是用Module做namespacing，用Class作實例化。module關鍵字會產生Module實例，class關鍵字會產生Class實例。

SomeClass只不過是object，而SomeClass的名字只是常數。

Constant與一般變數不同的地方是Constant具有scope，讓程式結構像資料夾一樣。

```
MyConstant = "Outside module"

module MyModule
  MyConstant = "Outer constant"
  ::MyConstant                     #=> "Outside module"
  class MyClass
    MyConstant = "Inner constant"
    ::MyConstant                   #=> "Outside module"
  end
end

MyModule::MyConstant               #=> "Outer constant"
MyModule::MyClass::Myconstant      #=> "Inner constant"

module M
  class C
    module M2
      Module.nesting               #=> [M::C::M2, M::C, M]
    end
  end
end
```


















=================================================


### Monkeypatch的問題與refinement

打開class可以創建新的方法，但如果方法名已經存在，就會覆寫掉原本的方法，(Monkeypatch)在處理標準庫時要注意

```
class A
  def foo
    "bar from foo"
  end
end

class A
  def foo
    "rewrite foo"
  end
end

A.new.foo #=> "rewrite foo"
```

class被reopen以後，所有的方法會同時被改寫，我們也無法調用到改寫前的方法，refinement可以解決此問題。

```
class C
  def say_hi
    "before reopen"
  end
end

module SayHiRefinement
  refine C do    # 要使用refinement，只要定義時包上refine就可以了
    def say_hi
      "after reopen"
    end
  end
end

c = C.new
c.say_hi #=> "before reopen"

module A
  using SayHiRefinement
  c_in_a = C.new
  p c_in_b.say_hi
end
#=> "after reopen"

module B
  c_in_b = C.new
  p c_in_b.say_hi
end
#=> "before reopen"
```

詳見[魔法師的手杖](http://sibevin.github.io/posts/2016-05-02-163010-refinement-in-ruby)。

### 實例變量在顯式賦值後才會出現

class 實例化之前不會有實例變量，唯有實例化之後，且執行到定義實例變量的地方時，實例變量才會真正被建立出來，注意，實例變量初始化時不可不賦值。

```
class A
  def initialize
    @var_1 = 1
  end

  def gen_var_2
    @var_2 = 1
  end
end

a = A.new
a.instance_variables #=> [:@var_1]
a.gen_var_2
a.instance_variables #=> [:@var_1, :@var_2]
```

### object 的本質

對象的本質是***實例變量（object.instance_variables）***、***對自身class的引用（object.class）***、***對象的唯一標識符（object.object_id）***。

因為方法會被所有實例共用，因此不存在對象中，而在class中。透過對類的引用來取得。可以用`#methods`查詢對象的方法。

類裡面有類方法和實例方法。這些方法不存在對象中，而在對象的class中。

### class 的本質

***class的本質是Class的實例（SomeClass.class）***、***一組實例方法（SomeClass.instance_methods）***、***和對超類的引用（SomeClass.superclass）***。

換言之，所有的類方法，都是class Class的實例方法。

Ruby 中所有東西都是對象，任何的 class C 都是 Class 這個類的實例。
```
String.class #=> Class
Object.class #=> Class
Class.class #=> Class
```

因此任意的 class A 的類方法都是 Class 這個類的的實例方法，大推。
```
Object.methods(false) == Class.instance_methods(false) # false表示不列出繼承來的方法
#=> true
```

BasicObject 是所有類的祖先，Class則繼承於Module，Module提供了new(), allocat(), superclass()等實例方法給Class使用。

魔法師的手杖的[一張圖](http://sibevin.github.io/images/post/20160410155612-mr2-ch2-object-model.png)說盡所有事。

class 的名字是常量。object 是變量。

### 常量

大寫開頭的即是常量。在Java和C#中，任意的class也是Class的實例，但類和對象有很大的差別，類的功能也比對象有限。不能在運行時創建一個類，或修改類方法。對Java和C#，Class實例比較像是描述符，而非真正的類。

常量（如Module和Class）像是文件系統，可以提供樹狀結構。下面同一縮排表示同一層資料夾。

```ruby
module M
  class C
    X = "Constant"    # M::C::X
  end

  X = "M constant"    # M::X
end

Module.constants    # 回傳所有頂級常量，包含class名
Module.nesting      # 獲得當前常量的路徑
```

一個module基本上就是一組實例方法，而class則是增加若干新方法的module，繼承於module。兩者的差異處，在於使用module主要是***提供命名空間***（如上例）和希望將來***被include進某個class中***，而class則是將來希望能被實例化或是被繼承。

### require & load

`load('xxx.rb')`可執行xxx.rb的內容，預設情況會將執行後的常量和變量都留下來，汙染當前的命名空間，若要避免被汙染，可以用

`load('xxx.rb', true);`

如此會將xxx.rb的內容全部用一個匿名Module包住，執行完成後，會被銷毀。

`require('./xxx')`是用來導入類庫的，而這些類名是你導入時就希望得到的，所以通常你不會須要銷毀它。

兩者還有一些差異

* require可以不加副檔名，load一定要加
* require要指定相對路徑（例中為./），load則不一定
* require若載入相同檔案兩次，只會載入並執行第一次，load則是每次載入都會執行。

### 方法查找

執行一個方法時，Ruby會做兩件事（沒有例外）：

1. 從ancestors找到這個方法，稱為***方法查找***。
2. 將***self***作為receiver並執行該方法。

receiver是調用方法所在的對象。ancestor則是一個包含class和module的繼承樹。當執行一個方法時，Ruby會先在receiver中找該方法，然後一層一層往ancestor鏈上游找去，直到找到或拋出例外為止。

```
class A
end

A.ancestors  # [A, Object, Kernel, BasicObject] 查詢 A 的 ancestors
```

其中，Kernel或是其他自定義的module，當被include進其他class的時候，會創建一個封裝該module的匿名class，並插入到ancestors中，位置在被插入的class的上方。舉例

```
module M
  def method_a
    "method_a in M"
  end
end

class C
  include M
end

class D
  prepend M
  def method_a
    "method_a in D"
  end
end

C.new.method_a #=> "method_a in M"
D.new.method_a #=> "method_a in M"
C.ancestors #=> [C, M, PP:ObjectMixin, Kernel, BasicObject]
D.ancestors #=> [M, D, PP:ObjectMixin, Kernel, BasicObject]
```

與`include`相反，使用`prepend`時，module會放在被插入的class下方。

規則很容易理解，include的modules會照include的順序放在class的最上方（會被class內部定義蓋過），而prepend的modules會照prepend的順序放在class的最下方。

```
module M1; end
module M2; end
module M3; end
module M4; end
class C
  include M1
  prepend M3
  include M2
  prepend M4
end

C.ancestors #=> [M4, M3, C, M2, M1, Object, PP::ObjectMixin, Kernel, BasicObject]
```

module被封裝進一個匿名的class，這樣的include class，`superclass()`不會察覺。

另外值得一提的是，Kernel常被用來封裝使用者自定義的方法。建議把方法都包在這裡。如果包在Object裡，很可能會汙染Object原有的方法。如果包在Kernel module，同名方法會被Object蓋過去。

一樣大推魔法師的手杖做的[方法查找](http://sibevin.github.io/images/post/20160511181400-mr2-ch2-method-lookup.png)圖示，一圖勝過千言萬語。

### self

執行一個方法時，Ruby會做兩件事（沒有例外）：

1. 從ancestors找到這個方法，稱為***方法查找***。
2. 將***self***作為receiver並執行該方法。

self是特殊的變量，保存的是當前的object。object調用method或實例變數都會使用self作為receiver。

只有兩種方式可以修改self的值,一是`.`，一是`class`關鍵字。

而這樣的程式碼，其實是在做三件事：

```
class Test
 doing something...
end
```

1. 定義常量Test
2. 生成新的Class實例並賦值給Test常量
3. 把self的值換成這個新產生的實例
4. 接下來一直執行到end為止

既然所有東西都是對象，而調用對象方法時self就會成為那個object，那我們在irb中還沒有調用任何方法前，self是什麽呢？self是一個叫作main的Object實例。這個對象有時被稱為頂級上下文top level context，是謂棧頂。

```
self #=> main
self.class #=> Object
```

self通常是接收到最後一個方法調用的對象來充當，但是在class和module定義中，方法定義之外，self由該class或module充當。

### private

了解self，就能了解private方法。

private方法由兩條規則控制：

1. 如果調用方法的receiver不是自己，就要明確指定receiver。
2. private方法只能被隱式的調用，不能顯式的指定receiver。

綜合兩個規則，private方法只能在自身中調用private方法。

```
class C
  def public_method_1
    self.private_method
  end

  def public_method_2
    private_method
  end

  private
  def private_method; end
end

C.new.public_method_1 #=> NoMethodError
C.new.public_method_2 #=> nil
```

可以使用superclass的private方法，因為無須顯式的指定receiver。
```
class C
  private
  def private_method; end
end

class D < C
  def private_method
    super
  end
end

D.new.private_method #=> nil
```





# Metapromgramming Ruby 方法

###### Dynamic Dispatch
```
class C
  def public_m1
    "public_m1"
  end
  def public_m2
    "public_m2"
  end
  def call_private_m
    private_m
  end
  private
     def private_m
      "private"
    end
end

c = C.new
c.public_m1               #=> "public_m1"
c.public_m2               #=> "public_m2"
c.private_m               #=> 無法顯示呼叫private method
c.call_private_m          #=> "private" 只有class內可以使用private method
c.send(:public_m1)        #=> "public_m1" 使用send動態的呼叫方法c.send(:private_m)        #=> "private" 使用send也可以呼叫private method
c.send(:method_m1, param1, param2, ...) 
# 方法名只要送入字串或symbol(preferred)即可，send方法把「選擇用哪個實例方法」的時間點延到執行時才決定。
```

###### Dynamic method
>所有的method都是procedure，所有的method name都是symbol

可以發現剛剛public_m1, public_m2有許多相似處，可以動態的定義方法如下：

```
class C
  ['public_m1', 'public_m2'].each do |m|
    define_method "#{m}" do
      "#{m}"
    end
  end
end

C.instance_methods(false)     #=> [:public_m1, public_m2] 帶入false則不會顯示繼承來的方法

# 以下方法仍可以正確呼叫
c.send(:public_m1)            #=> "public_m1"
c.send(:public_m1)            #=> "public_m2"
```

###### Ghost Method

當在ancestors中找不到方法，Ruby解釋器會在最初的receiver上調用method_missing()方法。因為method_missing是BasicObject的private instance method，所以一定會找到此方法。

```
class O
end

o = O.new
o.hihi               #=> NoMethodError: undefined method `hihi' for #<O:0x00000002765e60>

class O
  def method_missing(method, *args)
    puts "You are calling #{method} (#{args.join(',')}), but not this method, haha"
  end
end

o.jkjk(123,321)     #=> You are calling jkjk(123,321), but not this method, haha
```

再舉一例，製作類似attr_reader的功能，並補充一些事。

```
class C
  def public_mx
    "public_mx"
  end
  def method_missing(method, *args, &block)
    if method.to_s =~ /^public_m(\d*)$/
      return "public_m#{$1}".to_s
    else
      super
    end
  end
end

# method_missing可以捕獲public_m後面接任意數字的方法呼叫
c = C.new
c.public_m0
c.public_m1
...

# 但是無法被查詢到
c.respond_to?(:public_mx)   #=> true
c.respond_to?(:public_m5)   #=> false

# respond_to? 會去呼叫 respond_to_missing?，而 respond_to_missing?預設就是用來處理ghost method，理論上如果你建立了ghost method，要記得一並覆寫 respond_to_missing?，讓它對ghost method可以回傳true。 --- 魔法師的手杖

class C
  def respond_to_missing?(method, include_private = false)
    if method =~ /^public_m(\d*)$/
      return true
    else
      super
    end
  end
end

c.respond_to?(:public_m5)  #=> true

# 理論上你也可以覆寫methods方法，但是那會導致你可能產生好幾千個ghost method，這可能不是你想要的。
```

書中舉Bug Hunt例子說明使用method_missing時要很小心，最好有限度的定義出自己想要捕獲的method，因為任何找不到的方法，又會再次回到method_missing，直到溢出棧為止。

Ghost method優先權最低，如果希望用到ghost method不會被同名方法覆蓋，應該刪除那些繼承而來的方法。為了安全，應該在代理class中完成這件事。

可以用`undef_method`和`remove_method`移除已定義的方法

```
class C
  def to_s
    "New to_s"
  end
end

c = C.new
c.to_s                         # "New to_s"

class C
  remove_method :to_s          # 僅移除自己的方法  
end

c.to_s                         #=> "#<C:0x00000002733848>"

class C
  undef_method :to_s # 移除所有的，包含繼承來的方法
end

c.to_s                         #=> NoMethodError
```

或是直接繼承BasicObject，它是擁有最少實例方法的class對象，稱作blank slate class。

```
class C < BasicObject
end
```

小結：Ghost method用在串api（第三方有修改時本地不用改），會把所有找不到的方法都丟進去，而dynamic method都是定義已知但相近的method。簡言之，ghost method是比較hack的做法，且效率較差，盡可能用dynamic method。

Ruby有一些保留方法，長得像`__method__`，比如`__send__()`，其實是`send()`的alias，一些library會使用保留方法而不是常規方法，以防被使用者覆寫。














# Metaprogramming Ruby 代碼塊


代碼塊（Block）是控制作用域（Scope）的手段。

Block僅是可調用對象的一員，其他還有像proc，lambda這樣的對象。

目前為止講的都是Ruby物件導向的特性，然而在這一章講的block，觀念是繼承於functional programming language。




## 方法速查表

```
String.instance_methods        # 查看String class的實例方法
"abc".methods                  # 查看"abc"這個實例的方法，與上相等

String.instance_methods(false) # 只顯示非繼承而來的自有實例方法
"abc".class                    # 查看實例"abc"的class
String.superclass              # 查看String的父輩class

Module.nesting                 # 顯示當前路徑
Module.constants               # 顯示當前程式中所有頂層常數
SomeModule.constants           # 顯示此SomeModule的內部常數
```