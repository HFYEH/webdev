通常，類中的方法如不標明，會使用一隱式的self作為接收者。所以在下列兩個方法中，都可以取得變量`m`的值。然而，如果是要賦值給變量`m`，則必須顯示的調用，否則會被認為是局部變量，而無法賦值。

```
class C
  attr_accessor :m
  def with_self(value)
    puts m    # self.m
    self.m = value
  end

  def wo_self(value)
    puts m    # self.m
    m = value
  end
end

c = C.new
c.with_self("Hello from with_self")
c.m
# => "Hello from with_self"


c.wo_self("Hello from wo_self")
c.m
# => "Hello from with_self"

```