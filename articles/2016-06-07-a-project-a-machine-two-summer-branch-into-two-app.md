---
layout: post
title: 'capistrano一個專案一台機器兩個branch部暑成兩個app'
date: 2016-06-07 01:35
comments: true
categories: 
---
只要在deploy.rb裡改這段
```
# Default branch is :master, we deploy current branch
ask :branch, `git rev-parse --abbrev-ref HEAD`.chomp
set :branch, `git rev-parse --abbrev-ref HEAD`.chomp

if `git rev-parse --abbrev-ref HEAD`.chomp == "chiduino-prod"
  set :deploy_to, '/home/deploy/apps/chiduino'
elsif `git rev-parse --abbrev-ref HEAD`.chomp == "chiduino-dev"
  set :deploy_to, '/home/deploy/apps/chiduino-dev'
else
  break
end
```

這樣以後部署時就會抓當前的branch~

如果想要自行指定
可以打
`branch=my_branch cap production deploy`