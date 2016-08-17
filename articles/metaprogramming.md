# [Ruby和元编程的故事 - 第2回: 类与模块，Ruby的绝代双骄](https://ruby-china.org/topics/1581)

self是特殊的變量,保存的是當前的object,object調用method或實例變數都會使用self
只有兩個方法可以修改self的值,一是\(.\),一是class關鍵字

obj.method\(\)會把self的值切換為obj
class關鍵字做三件事

```
class Test
  doing somthing...
end
```

1. 定義常量Test
2. 生成新的實例並賦值給Test常量
3. 把self的值換成上一步產生的實例
  接下來一直執行到end


國外Metagramming教學網站
[Metaprogramming in Ruby](http://ruby-metaprogramming.rubylearning.com/)



======================
# Dynamic Dispatch
```
class A
  def ss
  end
end

a = A.new
a.ss  (reciever.method，所以叫作send)
```

```
class B
  def public_m
    private_m
  end

  private
  def private_m
    "lala
  end
end

b = B.new
b.public_m
b.private_m (fail)
b.send(:private_m)
```

private method只能用隱含的方式呼叫的method（無reciever）

但是send可以呼叫private method


# Dynamic Method

所有的method都是procedure

所有的method name都是symbol

Module#define_method
real example

印出method都找得到

# Ghost Method
改寫BasicObject#method_missing?

印出會找不到method

a.responde_to?(:say_hi)會找不到
因此還要覆寫responde_to_missing?給repond_to?

Ghost method優先權最低，如果要用到ghost method不會被同名覆蓋，可用
remove_method, undef_method

或者可用blank state

擁有最少method的物件稱為blank state，可以繼承BasicObject（要幹這件事時先把檔案保留下來）

可以用alias，方法不會消失，只會找不到，可以用alias呼叫

ghost method覆寫時要小心，不要沒有寫到該method，會一直挖method_missing，到too_deep，method應該要寫在外部，也可以避免挖太深的問題

ghost method用在串api（第三方有修改時本地不用改），會把所有找不到的方法都丟進去，而dynamic method都是定義已知但相近的method

ghost是比較hack的做法，盡可能用dynamic method









