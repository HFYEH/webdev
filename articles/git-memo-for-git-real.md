## Configuration

```bash
git config --global user.name "Username"
git config --global user.email "email"
git config --global color.ui true
git config --global core.editor vim
git config --global merge.tool opendiffgit config --global core.autocrlf input # Fix windows line endings that get introduced，把CR/LF全部換成LF，linux用
git config --global core.autocrlf true # 進入當前目錄時會換成window的CR/LF，進到commit時會自動換成LF，window用
git config --global core.autocrlf false # window only project

git config --list  // 檢視所有設定
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lol "log --graph --decorate --pretty=oneline --abbrev-commit --all"
git config --global alias.mylog "log --pretty=format:'%h %s [%an]' --graph"
git config --global alias.lol "log --graph --decorate --pretty=oneline --abbrev-commit --all"

// --global是此user，存在~/.gitconfig
// --local是此repo，存在repo的.git/config
// --system是所有user
```

## 裸裝與平裝的分別
```
git init project #單人使用(平裝)
git init --bare project.git #多人使用(裸裝)
```

## clone剛剛開好的裸裝repo

```
git clone /var/git/project.git/ #內部連線
git clone ssh://imroot@127.0.0.1:/var/git/project.git/ #外部連線
```

## git diff
```
git diff    #比較未 staging 與所在 branch 中最後一個 commit,等同 git diff HEAD
git diff --cached    #比較 staging area 跟 Repo
git diff HEAD^    #比較未 staging 與 Repo中倒數第二個 commit
```

想看先前的版本間差異
```
git log --oneline    #先顯示 log,方便後續操作
git diff SHA1..SHA2
git diff --since=1.week.ago --until=1.minute.ago
```

## git reset \(已經 git push後嚴禁使用\)
```
git reset HEAD filename #從 staging area 狀態回到 unstaging 或 untracked \(檔案內容並不會改變\)
git reset HEAD --soft HEAD^   # 把最後一次 commit 的檔案回復到 staging 並取消最後一次 commit

# 若只是忘記加入某檔案可以使用
git add filename
git commit --amend -m "message"

git reset --hard HEAD^    # 取消最後一個 commit 並且將專案目錄回覆到上一個 commit 的樣子
```
## git checkout
```
git checkout .    #Recover all to repo version
git checkout -- filename    回復為repo中的版本
git checkout branch_name    切換到 branch\_name 分支
git checkout v0.0.1    切換到具有'v0.0.1'的特定 commit
```

## git remote
```
git remote add <name> <address>   # 加入遠端 Repo
git remote -v`   # 檢視所有遠端 Repo
git remote rm <name>   # 刪除遠端 Repo
heroku create   # 創建新的遠端 Repo 於 Heroku 上並且在本地加入遠端 Repo

git remote show origin # 做下列三件事

# 1. 顯示所有 remote branch 及追蹤狀態
# 2. 顯示所有 local 及它們與 remote branch 的關係\(使用git pull會發生的事\)
# 3. 顯示所有 local 及它們與 remote branch 的關係\(使用git push會發生的事\)
```

## git push
```
git push -u name branch   # 上傳遠端 Repo 並且儲存此次設定
git push origin :branch_name   # 刪除遠端分支
git push --tag   # 為遠端 Repo 加上 tag
```

## git pull

###### 情境：如果遠端 Repo 與本機的 commit 不同,且不同處非同一個文件

以merge操作
```
git pull
# 會做兩件事
# 1. 同步遠端 Repo 至本機(git fetch)
# 2. 合併 origin/master (git merge origin/master)

# 此時 master 多一個 commit
# 加上新的commit後， origin/master 還不知道這個新的 commit
`git push`   # origin/master 與 master 指到相同位置,並且上傳至遠端 Repo
```

以rebase操作
```
git pull --rebase
# 會做兩件事
# 1. 同步遠端 Repo 至本機(git fetch)
# 2. rebase origin/master (git rebase origin/master)
git push
```

###### 情境：merge發生conflict
```
git pull     # 在合併時會出錯
git status   # 該文件會顯示 both modified
# 修改該文件...
git commit -a
git push
```
###### 情境：rebase發生conflict

```
git fetch
git rebase   # 預設會去rebase origin/branch_name

# 碰到衝突有三種處置方法
# 1. 解決衝突後 `git rebase --continue`
# 2. 跳過 master 上的這個 commit `git rebase --skip`
# 3. 回復到使用 rebase 前的狀態 `git rebase --abort`
```

### Local branch rebase \(無衝突情況\)
Rebase 真正含義

首先，當前的 branch 是從某個 commit 分出來的，那個 commit 就是此 branch 的 base。

Rebase 時,先把當前 branch 的最後一個 commit 到 base commit 之後的所有 commit 移到暫存資料區，再把HEAD指向要被 rebase 的目標（也就是跑了所有的新的base的commit）,再將暫存區中的 commit 一一 commit 回來

假定現在要從某branch做rebase master
```
git checkout branch_name        # 切到該branch
git rebase master               # 先在本 branch 上跑 master 的 commit, 再跑 branch 上的 commit
git checkout master
git merge branch_name
```

有了這層認識後，`git pull --rebase`就不難理解了。

當使用`git rebase`時，會找到當前branch的origin/branch，origin/branch和local branch有各自的更新，但是當前branch的base就是在上一次`git push`的地方。所以會把push後的local commit先移到暫存區，再跑所有的origin/branch的commit，最後再把暫存區的commit回來。

## Interactive rebase
在同一個分支裡跑 rebase 是在更改 commit 順序
```
# rebase 到 HEAD^
git rebase -i HEAD^   # 因為 HEAD^ 之後只有一個commit,故只會有一個 commit 出現，所以其實不會改變順序
git rebase -i HEAD~3    重跑最後三個 commit,會跑出 editor,編輯完後再執行該 editor
# 之後會進editor，編輯順序，不過要注意，最上面是最先會被commit的，儲存後就會rebase了，進入editor後可看說明更改順序和commnet，合併，拆解
```

## git branch
```
git branch                  # 列出目前的local branch
git branch -r               # 列出目前的remote branch
git branch -a               # 列出目前的local branch 和remote branch

git branch branch_name      # 建立一個分支 branch\_name
git checkout branch_name    # 切換到 branch\_name 分支
git checkout -b branch_name # 建立分支並切換到該分支 (常用）

git branch -d branch_name   # 刪除 branch\_name 分支

git branch -D branch_name   # 強制刪除 branch\_name 分支\(有 commit 但未 merge 時用\)
```

## git log
我比較常用GUI或是一開始設定的alias看log
```
git log --pretty=oneline   # 一個 commit 只顯示一行
git log --oneline -p`       # 將所有 log 和修改過的檔案內容列出
git log --oneline --stat --summary  #  查每個版本間的更動檔案和行數
git log --oneline --graph   # 圖形化
git log --until=1.minute.ago   # 只顯示一分鐘前的所有 commit
`git log --since=1.day(hour/month).ago   # 只顯示一天\(小時\/月\)以內的所有 commit
```

## git tag \(用於釋出版本\)
使用時機：每次推向production時使用（除非是用CI）
```
git tag                               # 檢視所有 tags
git tag -a v0.0.3 -m "version 0.0.3"  # 加上tag
git push --tags                       # 將本地的所有tag推到為遠端 Repo
git checkout v0.0.1                   # 切換到具有'v0.0.1'的特定 commit
```

## git blame

```
git blame filename --date short  # 關於此檔案的所有 commit 紀錄\(包含作者,日期,更動的行及其內容\)
```

## 建立遠端 branch
滿足條件

1. 須要別人協作自己的 branch
2. 存在超過一天的分支,想儲存在遠端 Repo

```
git checkout -b branch_name   # 建立並切換分支
git push origin branch_name   # 連結近端 branch 到遠端 branch 並追蹤
# 修改...
git commit -am "message"
git push                      # 會自動儲存到遠端 Repo 的該 branch
# 其他人只要 git pull 就可以同步了
```

## 追蹤遠端 branch
如果是別人開的遠端分支要我們加入
```
# 兩者同樣功效，都是新建一個branch，追蹤遠端分支，並切換過去
git checkout -b branch_name origin/branch_name
git checkout --track origin/branch_name
```

## stashing
正在分支編寫,臨時要去 master 救火
```
git stash save "message"   # 把目前還沒有 commit 的文件存到暫存區並且將工作目錄復原到最後一次 commit

# 救火完成後...
git checkout branch_name      # 切回剛剛寫到一半的分支
git stash apply               # 回到 stash 前的未 commit 狀態

# 進階stash
git stash save --keep-index   # 只把 staging 的檔案 stash
git stash save --include-untracked    上述指令只考慮 tracked file,如要把 untracked file 也 stash 要用這個

git stash -h                  # 可以查到所有功能
# 每次跑 stash 都會把 stash 存到 stash stack 中
git stash list                # 顯示所有 stash,從最新到最舊
git stash show                # 顯示最新的 stash 其中的變更內容
git stash apply stash@{1}     # 回復最新的 stash
git stash drop                # apply 後 stash 依然存在,使用此指令刪除最上層的 stash
git stash clear               # 清除所有 stash

# shortcut (常用）
git stash                     # git stash save
git stash apply               # git stash apply stash@{0}
git stash drop                # git stash drop stash@{0}
git stash pop                 # git stash apply; git stash drop;
```

## 清理遠端branch和本地的origin
```
git push origin :branch_name # 刪除遠端分支
git remote show origin   # 可以看到被砍掉的 branch 狀態為 stale，此時local branch還在，但是origin裡的branch失去追蹤的branch了
git remote prune origin   # 清理origin中被沒有追蹤的分支
```

## 在 Heroku 部署 local 分支
```
# Heroku 只會部署 master
<commit position>:<remote branch>

git push heroku-staging local_branch:master   # 把 local 分支部署到 Heroku master
```

## exclude \(專屬自己的資料夾,不給別人看的\)
```
.git/info/exclude
experiments/    # exclude folder
tutorial.mp4    # file
*.mp4                    # all file ended with .mp4
```

## .gitignore \(所有repo中都有的,要故意忽略的\)
```
*.log
```

###### 情境：要將本來在repo的檔案停止追蹤但不刪除

```
git rm --cached development.log
# 之後再加入.gitignore
```

## git purge
有檔案要從所有歷史中完全移除的情況

1. 違反著作權
2. 大檔案移除

## cherry-pick
```
# 要把別的branch的特定commit複製一份到當前branch，複製過來的commit有不同的SHA
git cherry-pick 54ea45
# 要寫入不同的commit message
git cherry-pick --edit 54ea45
# 要把複數commit拉到staging area並且不要commit
git cherry-pick --no-commit 53131s 41235e
# 把原commit的SHA加進commit message中
git cherry-pick -x 54ea45
# 把當前的user資訊加進commit message
git cherry-pick --signoff 54ea45
```

## git modules
A git repo inside a git repo
```
# 將某個repo抓下來當成submodule，資訊會寫在.gitmodules
git submodule add git@example.com:sub_m.git
git commit -m "Add sub_m submodule"

# 要修改submodule
cd sub_m                    # 先進入該 submodule
git checkout master         # Submodule抓下來後並不會切到特定的branch，要自行指定
# 修改...
git commit -am "made changes..."
git push                    # 將submodule推上remote repo
# 因為parent repo會指向先前的submodule未更動前的commit，所以要回到parent repo再更新
cd ..
git add sub_m
git commit -m "Update something in sub_m"
```


## Github
```
# Update your fork

# 1. Add remote for upstream
git remote add upstream <path_to_repo>
# 2. Fetch changes
git fetch upstream
# 3. Merge them into master
git merge upstream/master master
# 4. Push to your remote
git push origin master
```

## Issue
```
git commit -m "This message will show up at issue #1"
git commit -m "Fixes #1"
git commit -m "closes #1"
git commit -m "resolves #1"
# 上述三個，當commit被merge回master時，會關閉issue
```