---
layout: post
title: '(Coursera) Active Record and Action Pack - Active Record'
date: 2015-10-28 11:54
comments: true
categories: 
---
# Introduction

使用 ActiveRecord 的先決條件:
1. ActiveRecord 必須知道如何存取資料庫(rails讀取時會讀config/database.yml獲取這些資訊)
2. 按照慣例,table名稱為複數,其對應的ActiveRecord::Base的subclass為單數
3. 按照慣例,ActiveRecord預期其table有primary key, 其名稱為id

# CRUD - read
```
# all, order 回傳 ActiveRecord::Relation object,可以chaining, 可以用.length
Person.al
Person.all.order(:name => :desc).to_a			# 回傳 Array object

# first 回傳 Array object,只能放在chaining的最後
Person.all.first	# chaining之後只會query第一個
Person.all[0]			# 會取出所有的資料後再取第一條出來,效能極差

# take, pluck 回傳 Array object,只能放在chaining的最後
Person.take(3)		# 從table隨機取三筆資料,回傳 Array object
Person.pluck(:name)		# 只取name欄位出來,回傳 Array object
Person.all.map {|person| person.name}		# 先取出所有資料再取其 name attribute,效能極差

# where (hash) 回傳 ActiveRecord::Relation object,可以chaining, 可以用.length
Person.where(:name => "John")		# 回傳 ActiveRecord::Relation
Person.where(:name => "John").first	# Narrow down the query
Person.where(:name => "John").pluck(:age)	# 取name為John的資料的age欄位出來

# find_by	回傳單一物件
Person.find_by(:name => "John")	# 回傳Person object,如果找不到,就回傳nil
Person.find_by!(:name => "John")	# 找不到時會拋出Exception

# limit, offset
Person.limit(3)						# 只選前三筆資料出來
Person.limit(3).offset(1)	# 只選三筆,從第二筆開始取
```

# CRUD - delete
```
delete(id)	# 不會將資料實例化,直接將該row的資料刪除
destroy(id)	# 會先實例化該資料,再執行callback,再刪除
```

