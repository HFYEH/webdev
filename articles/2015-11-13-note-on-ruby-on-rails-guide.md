---
layout: post
title: 'Note on Ruby on Rails Guide 命列列和routing'
date: 2015-11-13 11:10
comments: true
categories: 
---
# Rails 命令列
```
rails --task	# 查看目前可用的rake (Ruby make) 指令,內容皆有說明
#在rails console下,app可查routing和發request,helper可以查helper
app.root_path
app.get '/'
helper.content_tag :p
```

# Rails 路由
```
# 在routes.rb中
`get  '/patient/:id/edit' , to:  'patients#edit'`, as: 'foo'
# 代表網址列上`/patients/16`這樣的網址會被送到patients controller中的show action,而且會傳入params[:id] = 16
# 在console
app.foo_path(16)
> "/patient/16/edit"

# 同時定義多個資源
resources  :photos,  :books, 

# 單數資源,不需id的,只有一個資源的
resource :profile
# form_for無法處理單數資源,須自行指定path,如下
form_for  @geocoder, url: geocoder_path do |f|

# 嵌套路由
magazines has many ads
resources :magzines do
  resources :ads
end

```

