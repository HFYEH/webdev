---
layout: post
title: '理解Rails delegate'
date: 2016-06-13 02:14
comments: true
categories: 
---
兩個Model如果是繼承關係，可以用子類別可以繼承父類別的方法或特性，如果兩個Model沒有繼承關係，想要讓一個類別使用另一個類別的方法，則可以用delegate。

本文翻譯自此https://simonecarletti.com/blog/2009/12/inside-ruby-on-rails-delegate/
翻譯是為方便自己理解，建議直接看原文~





Delegation 是一個Ruby專案中常見的例子，如果你把proxies, mixins 和 composition 當成一種delegation模式的話。

Delegation 設計模式是讓物件或實例曝露一些性質，但這些性質其實是取用與當前實例相關連的其它實例的性質而來的。

常見技巧，當呼叫一個不存在的方法時會得到method_missing，然後回傳該呼叫到正確的handler。然而，這不總是好辦法。在Ruby中還有更好方法去做delegation。

Ruby標準函式庫中包含 Delegate module，為Delegation提供支援。不幸的，我發現它比傳統的做法更複雜，因此我從未用過它。

## ActiveSupport Delegate module

如果你的專案有`ActiveSupport`你可以有更簡便的方式做出delegation：`Module#delegate`擴充。你可以使用它提供的`delegate` 模組在你的類別或模組裡，可以delegate一個方法給一個關連的實例。

舉例來說，考慮`Post` belongs_to a `User`.

```ruby
class Post < ActiveRecord::Base
  belongs_to :user
end

class User < ActiveRecord::Base
  has_many :posts
end
```

你也許想要呼叫`post.name`，並讓它回傳`user.name`，該user為post的關連實例。通常來說，你須要創建一個`name`方法。如下所示：

```ruby
class Post
  belongs_to :user

  def name
    # let's use try to bypass nil-check
    user.try(:name)
  end
end
```

相同的程式可以用`delegate`方法表示。惟須注意，delegate的方法要是public。

```ruby
class Post
  belongs_to :user
  delegate :name, :to => :user, :allow_nil => true
end
```

`delegate`方法可以被使用在任何情境，不只侷限於ActiveRecord models。舉例來說，你的 queue wrapper 可以 delegate 到內部的 queue implementation 的特定方法。

```ruby
class QueueManager

  attr_accessor :queue

  # Delegates some methods to the internal queue
  delegate :size, :clear, :to => :queue

  def initialize
    self.queue = []
  end

end

m = QueueManager.new
m.size
# => 0
m.clear
# => []
```

方法可以被 delegated 到實例變數，類別變數或常數（使用symbol）。`:to` 之後至少須要接一個方法。


## Options

`delegate` 方法可以加入一些選項來客製特性。

這是我最愛的，`:prefix`可以設定為`true`使得關聯的實例會出現在方法名稱中。你也可以自行設定prefix。

```ruby
class Post
  belongs_to :user

  delegate :name, :to => :user, :prefix => true
  # post.user_name

  delegate :name, :to => :user, :prefix => "author"
  # post.author_name
end
```

`:allow_nil`選項允許類別delegate該方法到一個nil實例中。在此情況下，該方法會回傳`nil`。預設行為是發出`NoMethodError`。

```ruby
class Post
  belongs_to :user
  delegate :name, :to => :user, :prefix => true
end

Post.new.user_name
# raise NoMethodError

class Post
  belongs_to :user
  delegate :name, :to => :user, :prefix => true, :allow_nil => true
end

Post.new.user_name
# => nil
```

`:to`不是選項，是強制須要輸入的東西。


## Documentation
作者建議直接看[source code](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/core_ext/module/delegation.rb)

