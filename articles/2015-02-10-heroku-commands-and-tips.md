---
layout: post
title: 'Heroku commands and tips'
date: 2015-02-10 09:23
comments: true
categories: 
---
在workspace下
`heroku rename rails-tutorial-hello`

資料庫遷移
`heroku run rake db:migrate`

使用Ruby產生隨機網址,只有給出連結別人才連得到
`('a'..'z').to_a.shuffle[0..7].join`

產生ssh key
`cat ~/.ssh/id_rsa.pub`

已上傳bitbucket & Cloud9

Uing SSL
```
# In config/environment/production.rb
config.force_ssl = true

```