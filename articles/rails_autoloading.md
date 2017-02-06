# Rails Autoloading

- [Rails Autoloading](#rails-autoloading)
    - [從常數查詢開始](#從常數查詢開始)
        - [Module.nesting](#modulenesting)
        - [Module.nesting.first.ancestors](#modulenestingfirstancestors)

## 從常數查詢開始

摘自[Constant lookup in Ruby](http://cirw.in/blog/constant-lookup.html)一文，Ruby class是常數，常數的搜尋是依下列步驟

1. Module.nesting
2. Module.nesting.first.ancestors
3. Object.ancestors 若Modeul.nesting.first為nil或module

用我的白話總結，找常數時*先找當前程式區塊形成的Module.nesting，再找當前位置的祖先，但如果當前位置的Module.nesting是空的或是一個Module，就直接去Object.ancestors找*。

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
這段程式中，碰到`B`時，它會先去`A::C::D`找，再去`A::C`，再找`A::B`，如Module.nesting顯示的那樣。

Namespace鏈可以在runtime用`Module.nesting`（內省introspectable機制）得到。

```ruby
module A
  module B
    Module.nesting == [A::B, A]
  end
end
#=> true
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
```ruby
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

## Object.ancestors

如果`Module.nesting == []`表示在頂層物件`Object`。Ruby會在該物件和其祖先找常數。以下例而言，所有常數都出現在Object這一層。

```ruby
class Object
  module C; end
end
C == Object::C

module B; end
B == Object::B
```

最後，雖然module的祖先沒有寫出來，但Ruby假設你會將module混入其他繼承於Object的類，所以會自動將Object.ancestors加到module的常數查詢鏈中。

```ruby
module A; end
module B
  B.ancestors == [B]
  A == Object::A
end
```

## About class_eval and singleton

如果是使用singleton，因為其繼承鏈不包含當前class，所以常數查詢也會找不到定義在該class的常數。

在class_eval時，會採用一樣的方式去找常數

```ruby
class A
  module B; end
end
class C
  module B; end
  A.class_eval{ B } == C::B
  B == C::B
end
```

然而，如果參數不是block而是String，就會用當前的程式區塊去找。

```ruby
class A
  module B; end
end
class C
  module B; end
  A.class_eval("B") == A::B
end
```

下例中，因為`C::D`的Module.nesting不為nil，所以找查詢的順序是先找`C::D`再找`C`(順序一），再找`C::D.ancestors`（順序二），又因為Module.nesting.first是Module，所以再去找到Object.ancestor（順序三），最後都沒找到，所以拋出例外。

```ruby
class A
  module B; end
end

class C < A
  class D
    B
  end
end
#=> NameError: uninitialized constant C::D::B
```

這個例子在順序二找到。

```ruby
class A
  module B; end
end

class C < A
  module B; end
  class D
    B
  end
end
#=> C::B
```

這個例子則是在順序三找到。

```ruby
class F; end

class E
  class G
    F
  end
end
#=> F
```