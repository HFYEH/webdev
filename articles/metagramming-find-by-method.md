---
layout: post
title: 'find_by_xxx'
date: 2015-07-18 12:03
comments: true
categories: 
---
不要用find_by_xxx
只是在做字串的過水,效率很差,請直接寫sql
```
2.2.2 :001 > class A
2.2.2 :002?>   def path(*argv)
2.2.2 :003?>     ap argv
2.2.2 :004?>     end
2.2.2 :005?>   def method_missing(method_name, *argv)
2.2.2 :006?>     method_name = method_name.to_s
2.2.2 :007?>     match = method_name.match(/(.+)_path/)
2.2.2 :008?>     if match
2.2.2 :009?>       path(match[1], *argv)
2.2.2 :010?>       else
2.2.2 :011 >         raise 'method_error'
2.2.2 :012?>       end
2.2.2 :013?>     end
2.2.2 :014?>   end
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
  Book Load (0.2ms)  SELECT  `books`.* FROM `books` WHERE `books`.`title` = '19' LIMIT 1
 => #<Book id: 21, title: "19", created_at: "2015-07-18 10:04:59", updated_at: "2015-07-18 10:04:59"> 
2.2.2 :009 > Book.find_by_id('19')
  Book Load (0.3ms)  SELECT  `books`.* FROM `books` WHERE `books`.`id` = 19 LIMIT 1
 => #<Book id: 19, title: "17", created_at: "2015-07-18 10:04:58", updated_at: "2015-07-18 10:04:58"> 
2.2.2 :010 > Book.find_by_created_at('2015-07-18 10:04:58')
  Book Load (0.2ms)  SELECT  `books`.* FROM `books` WHERE `books`.`created_at` = '2015-07-18 10:04:58' LIMIT 1
 => #<Book id: 18, title: "16", created_at: "2015-07-18 10:04:58", updated_at: "2015-07-18 10:04:58"> 
2.2.2 :011 >
```

***This kind of method is not efficient, so we need to use hash or sql***
`Book.where(:title=>'19').first`
`Book.where('id < 20').first`
 


