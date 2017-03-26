


此系列為Metaprogramming Ruby第二版的筆記，記錄於此作為複習之用。

# Metaprogramming in Rails

## The Design of Active Record

### A Short Active Record Example

```ruby
require 'active_record' 
ActiveRecord::Base.establish_connection :adapter => "sqlite3", :database => "dbfile"
```


## Active Support's Concern Module



## The Rise and Fall of alias_method_chain



## The Evaluation of Attribute Methods