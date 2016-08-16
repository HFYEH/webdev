# Metaprogramming Ruby 第一章對象模型

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

A.new.foo => "rewrite foo"
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
a.instance_variables => [:@var_1]
a.gen_var_2
a.instance_variables => [:@var_1, :@var_2]
```

###### object 的本質

對象的本質是***實例變量（object.instance_variables）***、***對自身class的引用（object.class）***、***對象的唯一標識符（`object.object_id`）***。

因為方法會被所有實例共用，因此不存在對象中，而在class中。透過對類的引用來取得。可以用`#methods`查詢對象的方法。

類裡面有類方法和實例方法。這些方法不存在對象中，而在對象的class中。

###### class 的本質

***class的本質是Class的實例（SomeClass.class）***、***一組實例方法（SomeClass.instance_methods）***、***和對超類的引用（SomeClass.superclass）***。

換言之，所有的類方法，都是class Class的實例方法。

Ruby 中所有東西都是對象，任何的 class C 都是 Class 這個類的實例。
```
String.class => Class
Object.class => Class
Class.class => Class
```

因此任意的 class A 的類方法都是 Class 這個類的的實例方法，大推。
```
Object.methods(false) == Class.instance_methods(false) # false表示不列出繼承來的方法
=> true
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

###### require & load

`load('xxx.rb')`可執行xxx.rb的內容，預設情況會將執行後的常量和變量都留下來，汙染當前的命名空間，若要避免被汙染，可以用

`load('xxx.rb', true);`

如此會將xxx.rb的內容全部用一個匿名Module包住，執行完成後，會被銷毀。

`require('./xxx')`是用來導入類庫的，而這些類名是你導入時就希望得到的，所以通常你不會須要銷毀它。

兩者還有一些差異

* require可以不加副檔名，load一定要加
* require要指定相對路徑（例中為./），load則不一定
* require若載入相同檔案兩次，只會載入並執行第一次，load則是每次載入都會執行。













