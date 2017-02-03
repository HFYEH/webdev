# Rails Autoloading

- [Rails Autoloading](#rails-autoloading)
    - [從常數查詢開始](#從常數查詢開始)
        - [Module.nesting](#modulenesting)
        - [Module.nesting.first.ancestors](#modulenestingfirstancestors)

## 從常數查詢開始

摘自[Constant lookup in Ruby](http://cirw.in/blog/constant-lookup.html)一文，Ruby class是常數，常數的搜尋是依下列步驟

1. Module.nesting
2. Module.nesting.first.ancestors
3. Object.ancestors 若上者為nil或module

### Module.nesting

第一，Ruby在包住自己的語法中尋找

```ruby
module A
  module B
  end
  
  module C
    module D
      B == A::B
      puts Module.nesting
    end
  end
end
#=> true
#=> A::C::D
#=> A::C
#=> A
```
這段程式中，碰到`B`時，它會先去找`A::C::D::B`，再找`A::C::B`，再找`A::B`，如Module.nesting顯示的那樣。

Namespace鏈可以在runtime用`Module.nesting`（內省introspectable機制）得到。

```ruby
module A
  module B
    Module.nesting == [A::B, A]
  end
end
```

因此如果嘗試直接下例，會找不到。因為在`A::C`的語法塊內，找不到`A::B`。

```ruby
module A
  module B; end
end


module A::C
  puts Module.nesting
  B
end
#=> A::C (沒有A)
#=> NameError: uninitialized constant A::C::B
```
### Module.nesting.first.ancestors

第二步，如果常數無法被在`Module.nesting`裡被找到，Ruby會在尋找當前的module或class的祖先。
```
class A
  module B; end
end


class C < A
  puts Module.nesting == [C]
  puts Module.nesting.first.ancestors == [C, A, Object, PP::ObjectMixin, Kernel, BasicObject]
  B == A::B
end
#=> true
#=> true
#=> true
```


