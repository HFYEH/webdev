# Metaprogramming Ruby 第一章筆記

##### Open class

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

##### 打開class可以創建新的方法，但如果方法名已經存在，就會覆寫掉原本的方法，(Monkeypatch)在處理標準庫時要注意

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

#####　實例變量

class實例化之前不會有實例變量，唯有實例化之後，且執行到定義實例變量的地方時，實例變量才會真正被建立出來，注意，實例變量初始化時不可不賦值。

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

##### 方法

可以用`Object#methods`查詢對象的方法。然而，對象其實僅包含它的實例變量和對象的唯一標識符（可透過`Object#object_id`查詢），方法因為會被所有實例共用，因此存放在class中。



