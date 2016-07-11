---
layout: post
title: '一個專案部署成兩個Heroku Apps'
date: 2016-05-21 12:45
comments: true
categories: 
---
# Branch設定：
# 現在新增兩個branch，chiduino-dev and chiduino-prod
# 以後不用master branch開發，開發的主線變為chiduino-dev
# 開發上所有功能都先merge到chiduino-dev，推上測試伺服器確定穩定後，
# 累積到一定的量，確定要更新到正式伺服器時才merge到chiduino-prod
# 下面兩個指令會把遠端的這兩個新的branch pull下來，你只須做一次
git checkout --track origin/chiduino-prod
git checkout --track origin/chiduino-dev
# 上面意思是新建一個local branch chiduino-dev，連結到origin的該chiduino-dev
# 連結後，以後你只要git push就會把local的chiduino-prod, chiduino-dev一起push
# 打git pull就會把remote的chiduino-prod, chiduino-dev一起pull
# 如果要針對特定branch pull or push
git push origin branch_name
git pull origin branch_name
​
# Heroku設定：
# development代號對應到的是開發用測試伺服器
# production代號對應到的是正式穩定版伺服器
# 輸入git remote -v可以看到目前有的git remote repository，
# 除了上述新增的兩個，應該還會看到origin，對應到的是bitbucket
git remote remove heroku
heroku git:remote -a chiduino -r production
heroku git:remote -a chiduino-dev -r development
​
# 部署到Heroku
# 了解以上的話，就會知道如何部署到兩個伺服器去
# 首先是開發伺服器
git push development chiduino-dev:master
# 再來是正式伺服器
git push production chiduino-prod:master
​
# 以下是比較正常的開發流程，你若懂可跳過
# 關於git merge
# 只是一個習慣。通常我們開發時會在別的branch開發，並在該branch先merge主線，再跳回主線merge該branch
# 比方我現在在hello branch上開發新功能，開發完後，我們要先merge chiduino-dev
git merge chiduino-dev
# 可能的conflict都在這邊解掉（才不會因為在主線解不掉結果爆掉）
# 然後切回chiduino-dev
git checkout chiduino-dev
# 再merge hello branch
git merge hello
# 這時只會fast-forward，接著推上線
git push development chiduino-dev
# 然後假設開發十幾個功能後，我們確定穩定後，就可以推正式伺服器了
# 一樣先merge一次
git merge chiduino-prod
# 切到chiduino-prod並merge回來
git checkout chiduino-prod
git merge chiduino-dev
# 推上線
git push production chiduino-prod