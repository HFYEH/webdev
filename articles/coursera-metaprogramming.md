# Metaprogramming

## Dynamic dispatch
```
class Dog
  def bark
    puts "Woof!!!"
  end
  def greet(greeting)
    puts greeting
  end
end
dog = Dog.new

# 可以用下列方式使用該方法
dog.bark	# call
dog.send("bark")	# send
dog.send(:bark)		# send
dog.send(:greet, "Hello")
method_name = :bark
dog.send method_name
```
```
pros = {name: "john", age: 15}
class Person; attr_accessor :name, :age; end
person = Person.new
pros.each {|key, value| person.send("#{key}=",value)}
person
 => #<Person:0x000000020c1028 @name="john", @age=15> 
```
不須要使用`.`呼叫method,用send即可
只要method有先定義好,即可使用使用`.send(string or symbol)`動態的呼叫方法,不須寫一堆條件判斷該呼叫哪個方法

## Dynamic method
格式,下式將產生一個instance method
`define_method :method_name &block`

In store.rb
```
class Store
	def get_piano_desc
  	puts "Excellent piano"
  end
  def get_piano_price
  	120.00
  end
  def get_violin_desc
  	puts "Excellent violin"
  end
  def get_violin_price
  	110.00
  end
end
```
In report_system.rb
```
require_relative 'store'
class ReportingSystem
	def initialize
  	@store = Store.new
  end
  def get_piano_desc
  	@store.get_piano_desc
  end
  def get_violin_desc
  	@store.get_violin_desc
  end
end
```
很多類似的方法,找出其pattern後,可改用dynamic method取代
```
require_relative 'store'
class ReportingSystem
	def initialize
  	@store = Store.new
    # 取得[:get_piano_desc, :get_violin_desc]
    @store.methods.grep(/^get_(.*)_desc/) {ReportingSystem.define_report_methods_for $1}
  end
  
  def self.define_report_methods_for (item)
  	define_method("get_#{item}_desc") { @store.send("get_#{item}_desc") }
    define_method("get_#{item}_price") { @store.send("get_#{item}_price") }
  end
end
```
減少重複
如果store有新的東西加進去,reporting_system自然就知道該method

## Ghost methods
Ruby使用method時,會先在自己的類別找該方法,再往其父類或module一直往上找,如果都沒有找到,就會呼叫method_missing
method_missing的預設動作是拋出NoNameError
因為method_missing只是一個method,我們可以在任意類別中改寫它
我們具有
1. 被呼叫的 method name
2. 所有被傳入的參數(包含block)

參考代碼
```
class Mystery
  def method_missing (method, *args)
    puts "Looking for..."
    puts "\"#{method}\" with params (#{args.join(',')}) ?"
    yield "Ended up in method missing" if block_given?
  end
end

m = Mystery.new
m.haha("abcde",123123) do |answer|
  puts "And the answer is: #{answer}"
end

m.haha("abcde",321321)
```

```
require_relative 'store'
class Reportingystem
	def initialize
  	@store = Store.new
  end
  def method_missing (name, *args)
    super unless @store.respond_to? (name)
    @store.send(name)
  end
end
```
須要用super的原因是,假如@store有該方法,就會進下一行,但一旦沒有該方法,就會一直在method_missing裡面繞,
加上super後,如果@store沒有該方法,則會呼叫@store的method_missing,而不是ReportingSystem的method_missing
