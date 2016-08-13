## Configuration

```bash
git config --global user.name "Username"
git config --global user.email "email"
git config --global color.ui true
git config --global core.autocrlf input
# git config --global core.editor vim
# git config --global merge.tool opendiff

git config --list  // 檢視所有設定
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lol "log --graph --decorate --pretty=oneline --abbrev-commit --all"

// --global是此user，存在~/.gitconfig
// --local是此repo，存在repo的.git/config
// --system是所有user
```

## 裸裝與平裝的分別


    git init project` #單人使用(平裝)

    git init --bare project.git` #多人使用(裸裝)


## clone剛剛開好的裸裝repo

```

git clone /var/git/project.git/ #內部連線

git clone ssh://imroot@127.0.0.1:/var/git/project.git/ #外部連線

```

## git diff

`git diff`    比較未 staging 與所在 branch 中最後一個 commit,等同 `git diff HEAD`

`git diff --cached`    比較 staging area 跟 Repo

`git diff HEAD^`    比較未 staging 與 Repo中倒數第二個 commit

想看先前的版本間差異

`git log --oneline`    先顯示 log,方便後續操作

`git diff SHA1..SHA2`

`git diff --since=1.week.ago --until=1.minute.ago`

## git reset \(已經 git push後嚴禁使用\)

`git reset HEAD filename` 從 staging area 狀態回到 unstaging 或 untracked \(檔案內容並不會改變\)

`git reset HEAD --soft HEAD^`    把最後一次 commit 的檔案回復到 staging 並取消最後一次 commit

若只是忘記加入某檔案可以使用`git add filename`和`git commit --amend -m "message"`加入並 commit

`git reset --hard HEAD^`    返回上一個 commit 並且回復檔案內容

## git checkout

`git checkout .`    Recover all to repo version

`git checkout -- filename`    回復為repo中的版本

`git checkout branch_name`    切換到 branch\_name 分支

`git checkout v0.0.1`    切換到具有'v0.0.1'的特定 commit

## git remote

`git remote add <name> <address>`    加入遠端 Repo

`git remote -v`    檢視所有遠端 Repo

`git remote rm <name>`    刪除遠端 Repo

`heroku create`    創建新的遠端 Repo 於 Heroku 上並且在本地加入遠端 Repo

`git remote show origin`

1. 顯示所有 remote branch 及追蹤狀態
2. 顯示所有 local 及它們與 remote branch 的關係\(使用git pull會發生的事\)
3. 顯示所有 local 及它們與 remote branch 的關係\(使用git push會發生的事\)

## git push

`git push -u name branch`    上傳遠端 Repo 並且儲存此次設定

`git push origin :branch_name`    刪除遠端分支

`git push --tags`    為遠端 Repo 加上 tag

### 如果遠端 Repo 與本機的 commit 不同,且不同處非同一個文件

`git pull`

1. 同步遠端 Repo 至本機\(git fetch\)
2. 合併 origin\/master \(git merge origin\/master\)
  \(此時 master 多一個 commit,但 origin\/master 不知道這個新的 commit\)

`git push`    origin\/master 與 master 到相同位置,並且上傳至遠端 Repo

或想以rebase操作
`git pull --rebase`

1. 同步遠端 Repo 至本機\(git fetch\)
2. rebase origin\/master \(git rebase origin\/master\)

`git push`

### 如果遠端 Repo 與本機的 commit 不同,且不同處是同一個文件

`git pull`    在合併時會出錯
`git status`    該文件會顯示 both modified
\(修改該文件...\)
`git commit -a`
`git push`

## git rebase

### 當想要合併遠端 Repo 但是產生衝突時除了用 merge 外,還可以用 rebase \(無衝突情況\)

`git fetch`    把遠端 Repo 與更新到本地

`git rebase`

1. 移動所有 master 的更變到暫存資料夾,這些更變是相對 origin\/master 而言
2. 跑所有 origin\/master 的 commit, 3. 跑所有在暫存資料夾中的 commit, 一次一個

### 當想要合併遠端 Repo 但是產生衝突時除了用 merge 外,還可以用 rebase \(有衝突情況\)

`git fetch`

`git rebase`

碰到衝突有三種處置方法
1. 解決衝突後 `git rebase --continue`
2. 跳過 master 上的這個 commit `git rebase --skip`
3. 回復到使用 rebase 前的狀態 `git rebase --abort`

### Local branch rebase \(無衝突情況\)

`git checkout branch_name`

`git rebase master`    先在 branch 上跑 master 的 commit, 再跑 branch 上的 commit

`git checkout master`

`git merge branch_name`

> Rebase 真正含義
> Rebase 時,先把自己的 commit 移到暫存資料夾,再把HEAD指向要被 rebase 的目標,再將原本自己的 commit 一一 commit 回來

## Interactive rebse

在同一個分支裡跑 rebase 是在更改 commit 順序

`git rebase -i HEAD^`    因為 HEAD^ 之後只有一個commit,故只會有一個 commit 出現

`git rebase -i HEAD~3`    重跑最後三個 commit,會跑出 editor,編輯完後再執行該 editor

## git branch

`git branch` 列出目前有多少 branch \(分支\)

`git branch branch_name`    建立一個分支 branch\_name

`git checkout branch_name`    切換到 branch\_name 分支

`git checkout -b branch_name`    建立分支並切換到該分支

`git branch -d branch_name`    刪除 branch\_name 分支

`git branch -D branch_name`    強制刪除 branch\_name 分支\(有 commit 但未 merge 時用\)

`git branch -r`    檢視所有遠端分支

## git log

`git config --global color.ui true`    設定顏色

`git log --pretty=oneline`    一個 commit 只顯示一行

`git log --oneline -p` 將所有 log 和修改過的檔案內容列出

`git log --oneline --stat --summary`    查每個版本間的更動檔案和行數

`git log --oneline --graph`    圖形化

`git log --until=1.minute.ago`    只顯示一分鐘前的所有 commit

`git log --since=1.day(hour/month).ago`    只顯示一天\(小時\/月\)以內的所有 commit

## git tag \(用於釋出版本\)

`git tag`    檢視所有 tags
`git checkout v0.0.1`    切換到具有'v0.0.1'的特定 commit
`git tag -a v0.0.3 -m "version 0.0.3"`
`git push --tags`    為遠端 Repo 加上 tag

## 合併分支至 master \(fast-forward merge\)

master 分支沒有新的 commit 的狀況

`git checkout master`

`git merge branch_name`

# git blame

`git blame filename --date short`    關於此檔案的所有 commit 紀錄\(包含作者,日期,更動的行及其內容\)

## Aliases

`git config --global alias.mylog "log --pretty=format:'%h %s [%an]' --graph"`

`git config --global alias.lol "log --graph --decorate --pretty=oneline --abbrev-commit --all"`

## 修復 master上的 bug 工作流程,再合併分支 \(recursive merge\)

master 分支有新的 commit 的狀況

`git pull`

`git branch -b branch_name`    開新分支

\(editing...\)

\(master 發生狀況...\)

\(將現有工作 commit 在該分支\)

`git checkout master`    切換回 master

`git pull`    確保目前版本為最新

\(修復 bug 並 commit 在 master\)

\(現在要合併...\)

`git merge branch_name`

\(跳出VI,產生新的 commit\)

## 建立遠端 branch

1. 須要別人協作自己的 branch
2. 存在超過一天的分支,想儲存在遠端 Repo

`git checkout -b branch_name`    建立並切換分支

`git push origin branch_name`    連結近端 branch 到遠端 branch 並追蹤
\(修改...\)

`git commit -am "message"`

`git push`    會自動儲存到遠端 Repo 的該 branch
其他人只要`git pull`就可以同步了

## Stashing

正在分支編寫,臨時要去 master 救火

`git stash save "message"`    把目前還沒有 commit 的文件存到暫存區並且回到最後一次 commit

救火完成後...

`git checkout branch_name`    切回剛剛寫到一半的分支
`git stash apply`    回到 stash 前的未 commit 狀態

`git stash save --keep-index`    只把 staging 的檔案 stash,其餘則否
`git stash save --include-untracked`    上述指令只考慮 tracked file,如要把 untracked file 也 stash 要用這個

> Quotation
> 每次跑 stash 都會把 stash 存到 stash stack 中
> `git stash list`    顯示所有 stash,從最新到最舊
> `git stash show`    顯示最新的 stash 其中的變更內容
> `git stash apply stash@{1}`    回復最新的 stash
> `git stash drop`    apply 後 stash 依然存在,使用此指令刪除此 stash
> `git stash clear`    清除所有 stash

shortcut

`git stash` = `git stash save`

`git stash apply` = `git stash apply stash@{0}`

`git stash drop` = `git stash drop stash@{0}`

`git stash pop` = `git stash apply; git stash drop;`

## 遠端 branch 被砍掉

自己的分支變成 local branch
`git push`    無遠端可 push

`git remote show origin`    可以看到被砍掉的 branch 狀態為 stale

`git remote prune origin`    清理被刪除的遠端分支

## 在 Heroku 部署 local 分支

Heroku 只會部署 master
`<commit position>:<remote branch>`

`git push heroku-staging local_branch:master`    把 local 分支部署到 Heroku master

## exclude \(專屬自己的資料夾,不給別人看的\)

```
.git/info/exclude
experiments/    # exclude folder
tutorial.mp4    # file
*.mp4                    # all file ended with .mp4
```

## ignore \(所有repo中都有的,要故意忽略的\)

```
*.log
```

## 停止追蹤但不刪除

`git rm --cached development.log`
之後再加入.gitignore

