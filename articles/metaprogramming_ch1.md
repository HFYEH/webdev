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

###### 對象的本質

可以用`object#methods`查詢對象的方法。然而，對象其實僅包含它的***實例變量（object.instance_variables）***和***對自身class的引用（object.class）***和***對象的唯一標識符***（可透過`object.object_id`查詢），因為方法會被所有實例共用，因此存放在class中。

###### class 的本質

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

在Java和C#中，任意的class也是Class的實例，但類和對象有很大的差別，類的功能也比對象有限。不能在運行時創建一個類，或修改類方法。對Java和C#，Class實例比較像是描述符，而非真正的類。


