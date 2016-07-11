---
layout: post
title: '(Coursera) Active Record and Action Pack - Advanced Querying'
date: 2015-11-02 11:12
comments: true
categories: 
---
# SQL Injection
```
# Normal query
People.where("email = '#{email}' AND password = '#{password}' ")
=>SELECT "people".* from "people" WHERE email = 'someemail' AND password = 'somepassword'
# SQL injection
password = "got you' OR 'x'='x"
People.where("emai = '#{email}' AND password = '#{password}' ")
=>SELECT "people".* from "people" WHERE email = 'someemail' AND password = 'got you' OR 'x' = 'x'
# 因為OR之後永遠為真，會傳出所有people資料
```

# 正確的使用SQL
```
# Array syntax - 要注意個數和順序,且如果query的內容有重複,都要打上~
People.where("email = ? AND password = ?", someemail, somepassword)

# Hash syntax - 變數改成用hash,最後再把hash設定進來即可
People.where("email = :email AND password = :password", :email => someemail, :password => somepassword)
```

