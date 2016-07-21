#Rails 指令介紹
##rails 新增前，新增後，請愛用 --help 去問它，server 與 console，
`rails --help`	#Give different outputs inside and outside rails project
##（new 時直接開好 mysql 支援，而非用預設的 sqlite ）
`rails new project1 --database=mysql`
## Use help to query
`rails generate --help`
`rails generate scaffold --help`

`gem list rails*`		See gem list with "rails" prefix

database.yml => yaml格式很重視縮排，不可多打或少打空白

##bundle 指令， Gemfile 與 Gemfile.lock 的關係
```
vi Gemfile
bundle install	#This will collect Gemfile into Gemfile.lock
```
Gemfile.lock為此一專案的所有gem的版本及其相依性的快照

##rake 指令， Rakefile ，使用 -T 來取得可用列表
`rake`help you do something, not in rails only
`rake -T`see all rake commands

##資料夾分佈與作用概觀（簡述）
##log：記錄，tail 的使用
##lib：資源庫
##test：測試
##public：檔案庫
##config：route / initializers / database / environment / locales
##app下的：models , controllers , views , helpers , mailers , assets
vender => seldom use
temp => cache, garbage
public => '/' you can see 404.html here
log => log
lib => ruby lib you wrote
db => migrate, schema(資料庫現況快照)
bin => exe (dont touch)
config.ru => rack header?? (don't touch)

##mysql的view可以用操作table的方式操作sql

config/initializers	=> 開啟rails時會跑此資料夾下所有rb檔


##啟動執行順序，與其作用（大略）
environment.rb > application.rb(Bundler) > environments/RAILS_ENV.rb  > initializers

##重大功能介紹（需繪圖+解釋）
route > controller + model > view + helper

#session ( request ) 生存範圍（以 #5.a 圖解釋）


#migration實作：
##change / up / down
##與 schema_migrations 版本對應（直接打 mysql 來做說明＋圖 #5.b ）
## 部分 magic_column（ created_at / updated_at / type 為保留字 ）
`rails g migration --help`
`rails g migration add_book`
dont use 'change'...use 'up' and 'down' method instead...
in database.yml modify sql password, databse

in add_book...
```
def up
	create_table :books do |t|
  	t.string :isbn
    t.string :title
  end
end
def down
  drop_table :books
end
```

Create database and do migration
`rake db:create` construct database
`rake db:migrate`	migrate database

`rails g migration add_book_age`
```
def up
	add_column :books, :age, :timestamp
end
def down
	remove_column :books, :age
end
```
`rake db:migrate`
`rake db:rollback`
回到某個版本
`rake db:migrate VERSION=xxxx`

資料庫所以知道現在的版本，是因為rails會migrate一個schema_migrations
In mysql
```
mysql -u root -p
use database_name;
show tables;
> schema_migrations
SELECT * FROM schema_migrations;
```
這裡面放的就是流水號，升板的是取沒有流水號的，從小的開始做，降版是從有的最大的開始往下做
現在的資料庫架構快照在shema.rb

rails g migration add_book_pages
```
def change
	add_column (:books, :page_count, :integer, {:limit => 2(byte), :defualt=> 0, :unsigned=>true, :null=>false})		#學習一次給詳盡完整
end
```
`rake db:migrate`

##model 與 relation
##callback（ before / after系列，自訂 ）
##基本 relation：belongs_to / has_many / has_one / has_many :through(穿過)（繪圖說明）

##rails下的一些methods
`rails c`
`123.days-22.days`

`rake db:drop db:create`
後重建migration(可連寫)
```
class InitTable < ActiveRecord::Migration
	def change
  	create_table :users do |t|
    	t.string :name
    end
    create_table :profiles do |t|
    	t.integer :user_id, :null => :false # 不可為空
    end
    add_index :profiles, [:user_id], :unigue => :true	# 增加取值效率,並同時限制此:user_id欄位值不可重複,第一參數可為array
    
    # 以下為一對多準備
    create_table :items do |t|
    	t.integer :user_id, :null => :false
      t.string :title
    end
    
    # 以下為多對多準備
    create_table :book_owners do |t|
    	t.integer :user_id
      t.integer :book_id
      t.integer :level
    end
    create_talbe :books do |t|
    	t.string :owner_type	#polymorphic association,解決一個table只能對應一個class的終極解法
      t.integer :owner_id	#polymorphic association,任何東西都可以當它的owner
    	t.string :title
    end
    
    # For STI (single table inheritance)
    create_table :animals do |t|
    	t.string :type	# type為特殊關鍵字,除STI不能用
      t.string :name
    end
  end
end
```
建model
user.rb
```
class User < ActiveRecord::Base
	has_one :profile
  has_many :item	#JC建議進了model後都不要加s
  has_many :book_owner
  has_many :book, :through => :book_owner
end
```
profile.rb
```
class Profile > ActiveRecord::Base
	belongs_to :user
end
```
item.rb
```
class Item < ActiveRecord::Base
	belongs_to :user
end
```
book_owener.rb
```
class BookOwner < ActiveRecord::Base
	belongs_to :user
  belongs_to :book
end
```
book.rb
```
class Book < ActiveRecord::Base
	has_many :book_owner
  has_many :user, :through => :book_owner
  belongs_to :owner, :polymorphic => true
end
```
animal.rb
```
class Animal < ActiveRecord::Base
end
class Cat < Animal
end
class Dog < Animal
end
class Kitty < Cat
end
```

開始玩~
```
rails c
user.create(name: "hello kitty")
User.find(1)	# 有的話會傳回User.id=1的物件,無的話會噴錯
User.find_by(id: 1)	#有的話會傳回User.id=1的物件,無的話會傳nil

profile = Profile.create(:user => user)	#物件存入資料庫並同時賦值給profile
profile.user	#belongs_to和has_many生出此method,可抓到user並回傳
User.find(profile.user_id)	#全等於上一個指令,而且如果沒有打belongs_to和has_many,一樣可以用這個指令找到

#用此法可存到全世界任何一個table(model)
book = Book.create(:owner => user, :title => "hihi")
=>#<Book id: 1, owner_type: "User", owner_id: 1, title: "hihi">
book.owner	#會給出class為user的model
=>#<User id: 1, name: "hello kitty">

#多對多,有三個user,三本book,讓第一個user擁有全部的book
User.create(name:"hihi")
User.create(name:"nono")
Book.create(title:"book1")
Book.create(title:"book2")
user = User.find(1)
Book.all.each do |book|
	BookOwner.create(:user => user, :book => book)
end; true	#加true避免印出
user.book
SELECT `books`.* FROM `books` INNER JOIN `book_owners` ON `books.`id` = `book_owners`.`book_id` WHERE `book_owners`.`user_id` = 1
# 自動做有一點點高級的sql, inner join
# 做到多對多就好,不要做多對多對多(用到表示有問題),盡可能用一對多或多對一就好,join會拖慢

#讓第三個user擁有第一本書
user = User.find(3)
BookOwner.create(:user => user, :book => Book.first)
user.book_owner
user.book

#polymorphic可以讓所有ActiveRecord成為其owner
book = Book.find(1)
book.owner
book.owner = book

# STI
Animal.create(name: "animal")
Animal.first
=>#<Animal id: 1, type: nil, name: "animal">	#注意type為nil
Cat.create(name: "cat")
=>#<Cat id: 2, type: "Cat", name: "cat">
Dog.create(name: "dog")
=>#<Dog id: 3, type: "Dog", name: "dog">
Kitty.create(name: "hello")
=>#<Kitty id: 3, type: "Kitty", name: "hello">
Animal.all
Cat.all
Kitty.all

# 自幹所有Relation,就跟ruby蓋code一樣
User.methods.class
=>Array
old_methods = User.methods	#把所有method存起來
class User
	has_many :food	#可以給一個完全不相干的東西
end
new_methods = User.methods #把新的所有method存起來
new_methods - old_methods	#差集,可以看到rails加了哪些新的method進去
#可以看到這些方法都是蓋出來的,rails蓋了很多這些method讓你很好用,換句話說,其實不用它幫你蓋,你也可以自建relation

#舉例
user = User.first
user.profile	#為has_one給的method
Profile.find_by_user_id(user.id)	#不用用他給的relation也可以找到
#不要太仰賴relation!

#ORM可以打純sql,檢查下列的關係,可以隨意調動,rails會自動重組
User.from('profiles AS p').joins('INNER JOIN users AS u ON p.user_id = u.id').where('p.id = 1').order('u.id DESC').limit(1).select('u.*')
```

#sql injection
`User.where("name = ?", 'myname')`

Pure sql
`ActiveRecord::Base.connection.execute('SELECT 1;')`


mysql is slow because parsing and latency
reduce the query times


# scaffold的全盤介紹與使用（ route > controller > action ( callback ) > view ）組成原因

# path的使用與實作（ 可附加“原因”：Ruby : method_missing 的範例）

#routes
`rake routes`
```
resoureces :sheeps do
	collection do
  	get :gogo
  end
  member do 
  	get :hihi
  end
end
```
`rake routes`
gogo doesnt need id
可自幹命名路由,多個命名路由可用member或collection組合而成,也可以一個一個自幹

#action callback（ before / after_filter : only , except ）

#提醒 jquery_ujs：讓傳統 http 支援 RESTful
在http header加入一些東西使得http可以完整支援CRUD

#我們來寫個 lib（於 lib/ 下，並用 application.rb 引入）


#寄封 mail 吧（遠端場會碰到 smtp 問題，此教學可先略過）

#多層 controller 製作（對外頁面 / 使用者後台 / 網站後台）





`render`
`redirect_to :action => 'index'`
in sheepscontroller
```
before_action :test1
...
...
...
private
def test1
	@gogo = 0000
end

```

in model
```
before_save :merge_address


private
def merge_address
	self.full_address = "#{self.city}#{self.area}#{self.road_number}"
end
```

in application template `<%= yield :top %>`
```
<% content_for :top do %>
yooo at top
<% end %>
```


in applicationhelper
```
def sheep_go(input)
	return input *3
end
```

in view
`<%= sheep_go(99) %>`


view 的幹法，好用的 helper
預設為 h( )，解除為 raw(  )（ 黑白名單機制 [ 版本提醒 ] ）
helper 來寫 view 的 methods
partial（ 底線開頭檔名 & local var 傳入 ）


```
t.timestamps = 
t.timestamp :updated_at
t.timestampe :created_at
```

# h and raw
```
<%= "<h1>yooo</h1>" %>
<%= raw("<h1>mooo</h1>") %>
<%= h("<h1>hooo</h1>") %>
```

h => blacklist, before rails3, the html will be inserted into html unless you use h, after rails3, it becomes a default setting

Source
```
&lt;h1&gt;yooo&lt;/h1&gt;
<h1>mooo</h1>
&lt;h3 &gt;hooo&lt;/h3&gt;
```




# find_by_xxx
不要用find_by_xxx
只是在做字串的過水,效率很差,請直接寫sql
```
2.2.2 :001 > class A
2.2.2 :002?> def path(*argv)
2.2.2 :003?> ap argv
2.2.2 :004?> end
2.2.2 :005?> def method_missing(method_name, *argv)
2.2.2 :006?> method_name = method_name.to_s
2.2.2 :007?> match = method_name.match(/(.+)_path/)
2.2.2 :008?> if match
2.2.2 :009?> path(match[1], *argv)
2.2.2 :010?> else
2.2.2 :011 > raise 'method_error'
2.2.2 :012?> end
2.2.2 :013?> end
2.2.2 :014?> end
```

```
2.2.2 :019 > require 'ap'
 => true
2.2.2 :020 > a = A.new
 => #<A:0x00000001fa9bb8>
2.2.2 :021 > a.yoo
RuntimeError: method_error
 from (irb):11:in `method_missing'
 from (irb):21
 from /home/sharefun/.rvm/rubies/ruby-2.2.2/bin/irb:11:in `<main>'
2.2.2 :022 > a.yoo_path
[
 [0] "yoo"
]
```

In rails console, find_by_xxx methods do the same thing!
```
2.2.2 :008 > Book.find_by_title('19')
 Book Load (0.2ms) SELECT `books`.* FROM `books` WHERE `books`.`title` = '19' LIMIT 1
 => #<Book id: 21, title: "19", created_at: "2015-07-18 10:04:59", updated_at: "2015-07-18 10:04:59">
2.2.2 :009 > Book.find_by_id('19')
 Book Load (0.3ms) SELECT `books`.* FROM `books` WHERE `books`.`id` = 19 LIMIT 1
 => #<Book id: 19, title: "17", created_at: "2015-07-18 10:04:58", updated_at: "2015-07-18 10:04:58">
2.2.2 :010 > Book.find_by_created_at('2015-07-18 10:04:58')
 Book Load (0.2ms) SELECT `books`.* FROM `books` WHERE `books`.`created_at` = '2015-07-18 10:04:58' LIMIT 1
 => #<Book id: 18, title: "16", created_at: "2015-07-18 10:04:58", updated_at: "2015-07-18 10:04:58">
2.2.2 :011 >
```

***This kind of method is not efficient, so we need to use hash or sql***
`Book.where(:title=>'19').first`
`Book.where('id < 20').first`


Rails DB connection and commands
```
ActiveRecord::Base.connection.tables
ActiveRecord::Base.connection.table_structure("projects")

config = Rails.configuration.database_configuration
host = config[Rails.env]["host"]
database = config[Rails.env]["database"]
username = config[Rails.env]["username"]
password = config[Rails.env]["password"]


```




