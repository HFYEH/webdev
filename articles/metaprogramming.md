# [Ruby和元编程的故事 - 第2回: 类与模块，Ruby的绝代双骄](https://ruby-china.org/topics/1581)

self是特殊的變量,保存的是當前的object,object調用method或實例變數都會使用self
只有兩個方法可以修改self的值,一是(.),一是class關鍵字

obj.method()會把self的值切換為obj
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


Class本身是一實例,如果用Class.new這個實例方法會產生新的Class實例,使用class關鍵字也是做一樣的事
Class的superclass如Object, BasicObject都是Class的實例(有點怪)
Test是Class的一個實例,Test.new會產生新的Test實例



http://sibevin.github.io/tags?t=mp


國外Metagramming教學網站
[Metaprogramming in Ruby](http://ruby-metaprogramming.rubylearning.com/)