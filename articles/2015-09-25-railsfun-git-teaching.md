---
layout: post
title: 'Railsfun Git 教學'
date: 2015-09-25 10:48
comments: true
categories: [git, railsfun]
---
# Before Class
##裝好 Rails，mysql-server，mysql 的 gem（由bundler裝）
##mysql server, lib-mysqlclient-dev (這個才能complie)

Mysql on Ubuntu `sudo apt-get install mysql-server mysql-common mysql-client libmysqlclient-dev`
Mysql Adapter `gem install mysql2`
libmysqlclient-dev裝這個才能compile mysql2 gem

ASCIIquarium demo


#Git 教學（因為要收作業，所以先教最基本的）
##裸裝與平裝的分別
```
git init project` #單人使用(平裝)
git init --bare project.git` #多人使用(裸裝)
```
##clone剛剛開好的裸裝repo
```
git clone /var/git/project.git/ #內部連線
git clone ssh://imroot@127.0.0.1:/var/git/project.git/ #外部連線
```
##基本操作(本地端更新,遠端repo更新)
```
echo "init" > init
git status
git add -A
git status
git commit -m "init_project"
git status
git push origin master
echo "yoo" > yoo
echo "moo" > moo
echo "hih" > init
git add -A
git status
git diff
git commit #會進編輯器打東西XD
git status
git push
```

##origin/master與遠端repo有不同的內容
clone另一個專案,修改內容並push,再回到原本的專案來,此時origin/master與遠端repo是不同的
```
git push		#會無法push,因為origin/master與遠端不同
git pull		#先更新origin/master,同時在both modified列出的檔案裡標示出須要手動修改
git status	#查看產生衝突的檔名
git diff		#查看衝突內容
# 修改內容~~
git add -A
git commit
git push
```

##原本的branch修到一半,要跳到別的branch作業
```
git branch 						#查
git branch new_job		#新增
git branch -d new_job	#刪除
git checkout new_job
echo "fix" > fix			#新增fix
git add -A
git commit -m 'fix_1'
git checkout master		#這裡沒有fix檔案
git checkout new_job
#習慣會solve in branch then merge into master
git checkout master
echo "modify" > modify
git commit -am "master modify"
git checkout new_job
git merge master
vi
git add -A
git commit
git checkout master
git merge new_job
git branch -d new_job
git branch new_job
git checkout newnew_job
#modify...
git add -A
git commit
git checkout master
git branch -d new_job
#error: there is one commit, if you want to delete, use -D
git push
#change other user
git pull
git log
git checkout 2c1d372.... (first node)
```

先在branch上merge master，再回到master merge branch