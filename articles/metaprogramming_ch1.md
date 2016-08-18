# Metaprogramming Ruby 第一章對象模型

此系列為Metaprogramming Ruby第一版的筆記，記錄於此作為複習之用。

###### Open class

Ruby在執行階段可以存取class內部資訊。亦即class是可以被打開的，而且是包函標準庫的所有class。

```
3.times do
  class C
    puts "Hello"
  end
end
```
每次執行時，都打開了這個class C，並且執行了`puts "Hello"`。第一次，C沒有定義，所以Ruby會定義，而內容會被執行，第二次和第三次，因為已經定義過了，Ruby只是打開它並執行裡面的語句而已。

class關鍵字比較像是作用域操作符，其任務是把你帶到class的上下文中，讓你可以在裡面定義方法。

###### Monkeypatch

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

###### 實例變量在顯式賦值後才會出現

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

###### object 的本質

對象的本質是***實例變量（object.instance_variables）***、***對自身class的引用（object.class）***、***對象的唯一標識符（object.object_id）***。

因為方法會被所有實例共用，因此不存在對象中，而在class中。透過對類的引用來取得。可以用`#methods`查詢對象的方法。

類裡面有類方法和實例方法。這些方法不存在對象中，而在對象的class中。

###### class 的本質

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

###### 常量

大寫開頭的即是常量。在Java和C#中，任意的class也是Class的實例，但類和對象有很大的差別，類的功能也比對象有限。不能在運行時創建一個類，或修改類方法。對Java和C#，Class實例比較像是描述符，而非真正的類。

常量（如Module和Class）像是文件系統，可以提供樹狀結構。下面同一縮排表示同一層資料夾。

```ruby
module M
  class C
    X = "Constant"    # M::C::X
    ::M::X    # 雙冒號表示根路徑，相當於在外層呼叫M::X
  end

  X = "M constant"    # M::X
  C::X    # C中的X
end

Module.constants    # 回傳所有頂級常量，包含class名
Module.nesting      # 獲得當前常量的路徑
```

一個module基本上就是一組實例方法，而class則是增加若干新方法的module，繼承於module。兩者的差異處，在於使用module主要是***提供命名空間***（如上例）和希望將來***被include進某個class中***，而class則是將來希望能被實例化或是被繼承。

###### require & load

`load('xxx.rb')`可執行xxx.rb的內容，預設情況會將執行後的常量和變量都留下來，汙染當前的命名空間，若要避免被汙染，可以用

`load('xxx.rb', true);`

如此會將xxx.rb的內容全部用一個匿名Module包住，執行完成後，會被銷毀。

`require('./xxx')`是用來導入類庫的，而這些類名是你導入時就希望得到的，所以通常你不會須要銷毀它。

兩者還有一些差異

* require可以不加副檔名，load一定要加
* require要指定相對路徑（例中為./），load則不一定
* require若載入相同檔案兩次，只會載入並執行第一次，load則是每次載入都會執行。

###### 方法查找

執行一個方法時，Ruby會做兩件事：

1. 找到這個方法，稱為***方法查找***。
2. 執行該方法。需要***self***。

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

module被封裝進一個匿名的class，這樣的include class，`superclass()`不會察覺。

另外值得一提的是，Kernel常被用來封裝使用者自定義的方法。建議把方法都包在這裡。如果包在Object裡，很可能會汙染Object原有的方法。如果包在Kernel module，同名方法會被Object蓋過去。

一樣大推魔法師的手杖做的[方法查找](http://sibevin.github.io/images/post/20160511181400-mr2-ch2-method-lookup.png)圖示，一圖勝過千言萬語。

###### self

執行一個方法時，Ruby會做兩件事：

1. 找到這個方法，稱為***方法查找***。
2. 執行該方法。需要***self***。

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

既然所有東西都是對象，而調用對象方法時self就會成為那個object，那我們在irb中還沒有調用任何方法前，self是什麽呢？

```
self #=> main
self.class #=> Object
```

