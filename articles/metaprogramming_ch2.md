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





改寫method_missing，並不會讓找不到的方法被#methods找到。Ghost method優先權最低，如果希望用到ghost method不會被同名方法覆蓋，可用`remove_method`, `undef_method`，或者可用blank state。擁有最少method的物件稱為blank state，可以繼承BasicObject（要幹這件事時先把檔案保留下來）。可以用alias，方法不會消失，只會找不到，可以用alias呼叫ghost method

覆寫時要小心，不要沒有寫到該method，會一直挖method_missing，到too_deep，method應該要寫在外部，也可以避免挖太深的問題

ghost method用在串api（第三方有修改時本地不用改），會把所有找不到的方法都丟進去，而dynamic method都是定義已知但相近的methodghost是比較hack的做法，盡可能用dynamic method。


國外Metagramming教學網站[Metaprogramming in Ruby](http://ruby-metaprogramming.rubylearning.com/)
