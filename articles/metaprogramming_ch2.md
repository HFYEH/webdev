# Metapromgramming Ruby 方法

###### Dynamic Dispatch

```
class C
  def public_m1
    "public_m1"
  end
  def public_m2
    "public_m2
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
c.public_m1         #=> "public_m1"
c.public_m2         #=> "public_m2"
c.private_m         #=> 無法顯示呼叫private method
c.call_private_m    #=> "private" 只有class內可以使用private method

c.send(:public_m1)  #=> "public_m1"  使用send動態的呼叫方法
c.send(:private_m)  #=> "private"    使用send也可以呼叫private method

c.send(:method_m1, param1, param2, ...)  # 方法名只要送入字串或symbol(preferred)即可，send方法把「選擇用哪個實例方法」的時間點延到執行時才決定。
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

C.instance_methods(false)    #=> [:public_m1, public_m2] 帶入false則不會顯示繼承來的方法

# 以下方法仍可以正確呼叫
c.send(:public_m1) #=> "public_m1"
c.send(:public_m1) #=> "public_m2"
```

書中的範例更複雜一些，此處略過不提。





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










國外Metagramming教學網站



[Metaprogramming in Ruby](http://ruby-metaprogramming.rubylearning.com/)

















