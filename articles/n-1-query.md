Why to use relation? Why not use relation?

`rails g scaffold book title`

## First layer: table
In migration file
```
class CreateBooks < ActiveRecord::Migration
  def change
    create_table :books do |t|
      t.string :title
      t.timestamps null: false
    end
    create_table :pages do |t|
      t.integer :book_id
      t.string  :name
    end
  end
end
```

`rake db:create db:migrate`

## Second layer: model
In model directory
`cp book.rb page.rb`

In page.rb
```
class Page < ActiveRecord::Base
  belongs_to :book
end
```

In book.rb
```
class Book < ActiveRecord::Base
  has_many :page
end
```
***Note: No 's' here, we don't want to generate a method with 's'***

## Third layer: view
```
...
<th>Title</th>
<th>all_page_word</th>
<th colspan="3"></th>
...
<td><%= book.title %></td>
<td><%= book.page.map{|i| i.name}.join(',') %></td>
<td><%= link_to 'Show', book %></td>
...
```
Now its N+1

## Generate 20 books each of them has 10 pages
`rails c`
```
2.2.2 :018 >  20.times do |i|
2.2.2 :019 >     book = Book.create(title:i)
2.2.2 :020?>   10.times do |j|
2.2.2 :021 >       Page.create(name:j, :book=>book)
2.2.2 :022?>     end
2.2.2 :023?>   end
```
Load page '/books' and see the log
```
  Book Load (0.3ms)  SELECT `books`.* FROM `books`
  Page Load (22.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 1
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 2
  Page Load (0.4ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 3
  Page Load (0.4ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 4
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 5
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 6
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 7
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 8
  Page Load (0.4ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 9
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 10
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 11
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 12
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 13
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 14
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 15
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 16
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 17
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 18
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 19
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 20
  Page Load (0.3ms)  SELECT `pages`.* FROM `pages` WHERE `pages`.`book_id` = 21
```
Here we encounter N+1 query
***Because pages are under books, to load pages, we have to do a lot of query***

The query algorithm is very quickly, mysql is slow because:
1. 80% of time are used to parse the mysql comment.
2. 2 times latency
So it's important to reduce query times as less as possible.

## To solve this, we use ruby way, not rails way
1. Pick up all ids

In books controller
```
  def index
    @book_pages = {}
    @books = Book.all
    book_id_sets = @books.map{|book| book.id}.join(',')		# get book_id_sets to string
    pages = Page.where("book_id IN (#{book_id_sets})").each do |page|
      @book_pages[page.book_id] ||= []
      @book_pages[page.book_id] << page
    end
  end
```
In index.html.erb
```
...
<td><%= book.title %></td>
<td><%= @book_pages[book.id] ? @book_pages[book.id].map{|page| page.name}.join(',') : '-' %></td>
<td><%= link_to 'Show', book %></td>
...
```
Now the query becomes
```
  Book Load (0.2ms)  SELECT `books`.* FROM `books`
  Page Load (0.2ms)  SELECT `pages`.* FROM `pages` WHERE (book_id IN (1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21))
```



# Coursera
```
Person.all.each do {|p| puts p.personal_info.weight}		# N+1 query!!

Person.include(personal_info).all.each do {|p| puts p.personal_info.weight}		# N+1 query!!
```


---
layout: post
title: 'sql的重新包裝利用'
date: 2015-07-18 12:35
comments: true
categories:
---
# sql重新包裝利用

 > N+1 query example

We have book and page models, if we want to retreive book from page
The last thing we need is book
`Book`
From page table
`Book.from('pages AS p')`
Filtering
`Book.from('pages AS p').where('p.id = 200')`
Use joins (like array join)
`Book.from('pages AS p').where('p.id = 200').joins('INNER JOIN books AS b ON p.book_id = b.id')`
Select (the result you want)
`Book.from('pages AS p').where('p.id = 200').joins('INNER JOIN books AS b ON p.book_id = b.id').select('b.*, p.name').limit(1)`

In rails console
```
book1 = Book.from('pages AS p').where('p.id = 200').joins('INNER JOIN books AS b ON p.book_id = b.id').select('b.*, p.name').limit(1)
book2 = Book.last
book1.name # has value
book2.name # error
```