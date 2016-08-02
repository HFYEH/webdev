---
layout: post
title: '(Coursera) Active Record and Action Pack - Expressing Database Relationships'
date: 2015-11-02 11:55
comments: true
categories: 
---
# One-to-one

### Database level
在migration內的 `t.references, index: true, foreign_key: true`
會產生 `CREATE INDEX "parent_table" ON "child_table" ("parent_table_id");` 這個SQL
(或使用add_index在migration中)

### Model level
在parent_table和child_table中各加入has_one和belongs_to

```
.build_xxx
.create_xxx
```
前者只建立在記憶體
後者直接建立在資料庫
但兩者都會把使用該method的實例的id移除(因為這筆新資料才要指向原擁有者,而不是舊資料)

# One-to-many
當parent被刪除時,可以用dependency設定其他所屬資料的處理方式
has_many :jobs, dependent: :nullify	# set foreign key to null (預設值)
has_many :jobs, dependent: :delete	# 刪除該子資料使用delete
has_many :jobs, dependent: :destroy	# 刪除該子資料使用destroy

# Many-to-many
```
# 假設現在已經有people table和Person model
rails g model hobby name
rails g migration create_hobbies_people person:references hobby:references	# 產生join table於database,但不用做model
# in migration
def change
	create_table :hobbies_people, id: false, do |t|
  	t.references :person, :index => true, :foreign_key => true
    t.references :hobby, :index => true, :foreign_key => true
  end
end
# in model
has_and_belongs_to_many
```

# Rich many-to-many
```
# person -> job -> salary_range, 三個 model,想知道一個person所有的salary_range
# 建立person和job的關係,再以job作為join table
rails g model salary_range, min_salary:float, max_salary:float, job:references

# in job.rb
belongs_to :person
has_one :salary_range

# in salary_range.rb
belongs_to :job

# in person.rb
has_many :jobs
has_many :approx_salaries, through: :jobs, source: :salary_range

# in person.rb 再加入
def max_salary
	approx_salaries.maximum(:max_salary)
end
# 可使用someperson.max_salary,會生出INNER JOIN的query,還不太了解through的功用
```

# Scope - 永遠回傳ActiveRecord::Relation (等同class method), 可以使query更具有表達力
default scope 預設的資料如何被取出來的method
```
class SomeModel < ActiveRecord
	default_scope {order :name}
end
在query中會自動插入ORDER BY
```
named scopes
```
# 格式為 scope :name, lambda

scope :order_by_age, -> {order age: :desc}
scope :starts_with, -> (starting_string){ where {"first_name LIKE ?","#{starting_string}%"}}
```

# Custom validation
```
validate :min_is_less_than_max		# single

def min_is_less_than_max
	if min_salary > max_salary
  	errors.add(:min_salary, "Error on value")		# min_salary為欄位名,第二參數為錯誤值
  end
end
```

