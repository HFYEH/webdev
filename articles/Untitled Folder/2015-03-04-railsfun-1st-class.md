---
layout: post
title: 'RailsFun 1st class (Ruby 1)'
date: 2015-03-04 11:06
comments: true
categories: [railsfun]
---
# Ruby Doc

core	不用裝,只要打開irb就有的
std-lib	不用裝,但要require才能用的
gem	要裝,並且要require才能用

gem	如 `gem i awesome_print`
在irb `require 'ap'`

#{irb} CallSystem 的兩種方式：`command` 與 system(“command”)
puts `ls`
system('ls')

# 不懂的先查詢
`class.class`	物件類別
`nil.methods`	物件方法
`nil.class.methods`	類別和物件方法
`nil.class.instance_methods`	物件方法
`nil.object_id`	查物件id
`nil.class.superclass`	查它老媽~

Fixnum/Bignum, Float, Array, String, Nil, True, False自己玩


```
"123" + "234" + "345" 
"#{123}#{234}#{345}"
"#{"123"}#{"234"}#{"345"}"
"#{123.to_s}#{234.to_s}#{345.to_s}"
```
`{#....}` 隱含 to string(不同的method,相同的結果)

symbol 的 `object_id` is a constant after announced
可以用`object.object_id`查看

#流程控制
||	回傳第一個為真的值,若全為假,回傳最後一個值
&&	回傳第一個為假的值,若全為真,回傳最後一個值
```
1 || nil || false || 0
 => 1 
0 || nil || false || 0
 => 0 
"hihi" || nil || false || 0
 => "hihi" 
nil || false || nil || false || 0
 => 0 
nil && false && nil && false && 0
 => nil
```


# 多if判斷(後置運筭)
puts "yooo" if 1 && 2 if 2 && 3
puts "yooo" unless nil unless false
從最後的條件判斷開始做


#三元判斷式
puts (false ? 123 : 234)
`x.is_a?(Integer)`

```
case x
	when 2
		puts 123
	else
  	puts 234
end
```


#methods
##methods定義
`def temp(x, y)`
return 值預設爲最後操作的 value，可自行 return

##參數預設值
有給預設值則不必定要輸入
```
def temp(a,b,c = 99)
   puts "#{a} , #{b} , #{c}"
end
```

##此順序不可逆不可交錯，因為預設值可不填，所以不可能在必要值前面就是了X"D
```
def temp(a,b,c=123,d=234,*argb , &block)
end
```

##複合參數
```
def temp(a,b,c = 99 , *argv)
	ap [a,b,c,argv]
end
```
使用陣列作為參數
兩者結果不一樣
`temp(*[1,2], *[3,4,5], 6, 7, 8, 9)`	＊會把陣列打散
`temp([1,2], [3,4,5], 6, 7, 8, 9)`	把陣列當一個變數欄位傳遞

method命名使用 ! / ? / = 的時機
Hash 省略大括號，參數省略小括號

#實作 Block，寫個類似 each 的 method
##Block：def temp(x, y, a = 1, b = 2, *argv, &block)
```
def temp(&block)
	block.call(99)
end
temp do |i| puts i; end
=>99
```
do...end即為block

##原理解釋（lambda { |x| puts x }）
lambda no name, wait for the call
`x = lambda{|a,b,c| puts "#{a} , #{b} , #{c}"}`
`x.call(1,2,3)`
`y = x`
`y.call(99,98,97)`


```
def temp(&block)
	block.call('hihi')
end

temp do |i|
	puts i*3
end
```

##Proc
```
x = lambda{ puts 123}
x.call
```

```
x = lambda{}
def temp(&block)
	ap block
end
```
`temp do ; end` or `temp {}` 進入空的block
`temp` `nil`
`temp(&x)` 進入x這個block

更多範例
```
x = lambda{ puts 123 }
x.call

x = lambda do
  puts 123
end
x.class
x.call

def temp(&block)
	block.call
end
temp do
	puts 123
end

def temp(&block)
	block.call(1)
end
temp do |i|
	puts i
end
```

#Variable Scope
@@ class variable => need to be initialized
@ instance variable
$ global variable
HI constant variable
區域變數
（constant variable覆蓋會噴 warning，常數 & 區域變數需事前宣告，其餘不用）
預設 $ 變數的正名：Std-lib 內的 English

#Variable Initialization
`@hooo = 123 unless @hooo`
`@hooo = 234 unless @hooo`
`@mooo ||= 123`

#Regular Expression
兩個練習網站
[Rebular](http://rubular.com/)
[RegexCrossword](http://regexcrossword.com/)(先上前六課)
regex = /^[A-Z][12][0-9]{8}$/
"A123456789".match(regex)

#module is a "class" cannot be "new"
如果不需要實例，則建module即足夠
```
module C
	def self.yoo
  	puts "call class methods"
  end
	def yoo
  	puts "call instance methods"
	end
end
```
mixin
```
class B
	include C   # already a file
end
```
`C.yoo`
`B.yoo`
`B.new.yoo`

單一繼承，self / super
Module 的定義以及作用（無法被 new），mixin（load & 當作 interface / template）

心法，蓋 code 為卍解（最快速修正別人 lib 的方式，載入順序為本），
##以 Nil 為例
```
class NilClass
	def hihi
  	puts "hihi"
  end
end
nil.hihi
```

##以Time的parse為例（require ‘time’）
time 在core和std-lib各有一份,parse是在std-lib裡定義的
Time.parse()
require 'time' => from stdlib
Time.parse("2014/12/12")

##以 gem 為例（acts_as_list {只看 github 的 source code}）
...

#傳遞訊息，Marshal 為卍解，Rails 中 session 的案例，object_id 的剖析
只要機器上有Marshal,就能成功且正確的解析出資料
```
y = {:wer =>123 , 'sdf' => Time.now , [] => [1,23,4]}	#超亂的資料
require 'json'
JSON.dump(y)						#丟出JSON
JSON.load(JSON.dump(y))	#讀進JSON,發現Time物件變成字串了...
Marshal.dump(y).length	#改用Marshal
Marshal.dump(y).class		#是字串
Marshal.dump(y)
y.object_id
x = Marshal.load(Marshal.dump(y))
x												# 成功復原
y												# 成功復原
x.object_id							# 兩者的object_id不同
y.object_id							# 兩者的object_id不同
```

#以下看不懂
`Marshal.dump(JSON)`		# 查看位置???
```
File.open('json.dump','w').write(Marshal.dump(JSON))
exit
irb
y = File.open('json.dump','r').read
Marshal.load(y)
requre 'json'
Marshal.load(y)
```

#例外處理 begin / rescue / retry / puts $!, $@
```
begin
	make_error
rescue
  puts "i get error"
end
```

```
begin
	make_error
rescue
	puts ap $!
	puts ap $@
	puts "i get error"
end
```

```
begin
	make_error
	rescue
	puts $!.class
	puts $@.class
end
NameError
Array
=> nil
```

##Enumerator
```
4.times do |i|
puts i
end
```

#Thread 使用與範例（+Mutex 做 race condition 的防止）
待補
```
4.times do |i|
Thread.new do
10.times do |i|
sleep rand*1
not done...
```

#ImageMagick
這玩意兒太好玩了!
`convert temp.txt temp.jpg`

#Nokogiri範例(不完整,自行照官方網站操作)
```
require 'open-uri' => http-client
body = open(url).read; true
body.lenght

require 'nokogiri'
html_doc = Nokogiri::
...
img_set = html_doc.search('img');true
img_set.each
`wget`
```

```
require 'open-uri'
```

# rotp 驗證之類的東東
rotp，解釋和應用（...拿出舊的永豐display信用卡...）
rqrcode，解釋和應用
rotp + rqrcode + google authenticator

# rqrcode 配合上面可以做qrcode驗證
```
require 'rqrcode'
qrcode = RQRCode::QRCode.new("blah")	# 將blah轉成qrcode
puts qrcode.to_s

```