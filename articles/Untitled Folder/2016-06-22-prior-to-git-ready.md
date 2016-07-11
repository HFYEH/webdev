---
layout: post
title: 'Git 會前準備'
date: 2016-06-22 01:12
comments: true
categories: 
---
## 先安裝Git
Client端要裝Git才能在本機端進行版本控制和程式碼上傳下載。

**Windos:**
下載後安裝
[Git for Windows](https://git-scm.com/download/win)

**Ubuntu:**
在terminal打
`sudo apt-get install git`

**Mac:**
使用圖形化界面的Git安裝程式，可從SourceForge下載：
http://sourceforge.net/projects/git-osx-installer/

想要順便裝圖形介面的，就自行google "git GUI for window/linux/OSX"吧~

[參考來源](https://git-scm.com/book/zh-tw/v1/%E9%96%8B%E5%A7%8B-%E5%AE%89%E8%A3%9DGit)

## 必要的設定
Ubuntu 和 Mac 在 terminal，windows 就開 git bash（程式集會有，或者任意資料夾按右鍵也有）
```
git config --global user.name "your_username"
git config --global user.email "your_email"
git config --global color.ui true
```

## 生成公開金鑰
這一步驟會在Client產生公鑰和私鑰，生成的私鑰會放在自己家，不被別人知道，而公鑰可以給其他人。公私鑰成對出現，公鑰加密過的東西只可用私鑰解密還原，被私鑰加密過的東西只可用公鑰解密還原。
我們會把公鑰提交給Bitbucket（一個雲端程式碼倉庫，五人以下的私有專案免費）和Github（公開專案免費），將來我們使用ssh協定傳送資料時，就不用每次再輸入密碼。我們也可以把公鑰傳給Raspberry Pi，這樣使用ssh連線時就可以不用輸入密碼，而且傳輸資料都會加密，比較安全。

簡單的原理說明：我們機器是A，要連線的機器是B，我們要做的是在A生成公私鑰，然後把A的公鑰記錄在B（在~/.ssh/authorized_keys文件中）。
1. A發起一個公鑰認證給B
2. B檢查是否存在該公鑰，沒有則斷連
3. B使用先前得到的A的公鑰對一組隨機字串加密，並回傳給A
4. A使用自己的私鑰解密，再回傳給B（實際上會結合session id生成MD5值才發出去）
5. B檢查回傳值是否是原先的隨機字串，是的話就完成認證（實際上會用同樣的方式生成MD5值才比較）


**Windows:**
先檢查是否已經有 /c/users/your_username.ssh 資料夾，如果有就不要再生成了，否則會被覆蓋。
開git bash，進入/c/users/your_username
`cd /c/users/your_username`
建立公私鑰
`ssh-keygen -t rsa -C "your_email@emailhost"`
之後就可以在
`c:\users\your_username.ssh`裡面找到公私鑰

Private Key - id_rsa 私鑰
Public Key - id_rsa.pub 公鑰

**Ubuntu:**
先檢查是否已經有 ~/.ssh 資料夾，如果有就不要再生成了，否則會被覆蓋。
`ssh-keygen`
如果不想輸入密碼可以enter兩次跳過，這樣就完成了。
公鑰位置預設在.ssh裡，打`cat ~/.ssh/id_rsa.pub`可以在terminal印出公鑰。


**Mac沒研究@@，JL加油~**


## 請上Github和Bitbucket申請帳號吧
[Github](https://github.com/)
[Bitbucket](https://bitbucket.org/)


## 下次開會就預設大家都做完上述工作了喔
雖然有時也會用圖形介面，但我最常用的還是command line，下次會講最重要的幾個指令，和git work flow，內容會選自下面這篇，不講太多，把握重要觀念，須要用到再查即可。
[Git指令](http://sharefun.logdown.com/posts/276649-git-memo-for-git-real)