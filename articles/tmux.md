
# zsh

[Using zsh and vim to Improve Terminal Visibility](http://mayurrokade.com/blog/using-zsh-and-vim-to-improve-terminal-visibility/)

[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)

[git plugin](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git)

# vim

[vimrc設定教學](http://wiki.csie.ncku.edu.tw/vim/vimrc)

[Vundle：Vim Plugin 自動下載、安裝、更新與管理工具（Vim Bundle）](https://blog.gtwang.org/linux/vundle-vim-bundle-plugin-manager/)

[ColorTheme](http://vimcolors.com/)

[所需即所获：像 IDE 一样使用 vim](https://github.com/yangyangwithgnu/use_vim_as_ide)

[笨方法学Vimscript](http://learnvimscriptthehardway.onefloweroneworld.com/)

[exVim](http://exvim.github.io/docs-zh/intro/)

# tmux

[[實用] 終端機 session 管理神器 - tmux](http://larry850806.github.io/2017/02/14/tmux/)

```
以下所有指令都在ctrl + b之後進行

tmux可以開啟server
一個server有許多session
一個session有許多視窗
一個視窗有許多區域

? 看說明 (M 代表要先按一下 Esc 鍵)
t 時鐘, 現在時間
: 指令模式
[ 捲軸
tmux ls 查目前session
tmux attach 進入上次的session
tmux attach -t session_count 選擇session編號進入
tmux kill-session -t session_count 依編號刪除session

****
Session
d 脫離目前session；暫時回到Shell界面，输入tmux attach能夠重新進入之前的session
D 選擇要脫離的session；若同時有多個sessions時使用
ctrl+z 關閉目前session
s 切換session

****
多視窗操作
c：建立新的視窗
n：移動到下個視窗
p：移動到上個視窗
w：列出目前 clinet 的視窗，用數字鍵選視窗
&：刪除當前的視窗
1：跳到指定的數字視窗
l：與先前選擇的視窗間切換
,：重新命名目前的視窗

****
單一視窗多區塊之操作

:set -g mouse on 使用滑鼠捲動區塊

%：當前區塊垂直切割成兩區塊
"：當前區塊水平切割成兩區塊
o：移往下一個區塊
x：刪除當前區塊
q：顯示區塊的數字代號，當數字顯示時使用數字鍵移往該區塊；
上下左右 跳到上下左右的分割視窗
ctrl按著 + 上下左右, 可以調整視窗大小
{：將目前的區塊移往前一塊移動
}：將目前的區塊移往後一塊移動
分區大小快速重排（假設已經切了四個分割區域）
alt 1 直的切割視窗排列
alt 2 橫的切割視窗排列
alt 3 上面一個大的橫視窗 + 下面直的三個直視窗
alt 4 左面一個大的橫視窗 + 右面直的三個直視窗
alt 5 四個視窗各 1/4

```

Cheet sheet
[https:\/\/gist.github.com\/andreyvit\/2921703](https://gist.github.com/andreyvit/2921703)

[http:\/\/blog.chh.tw\/posts\/tmux-terminal-multiplexer\/](http://blog.chh.tw/posts/tmux-terminal-multiplexer/)
[http:\/\/blog.lyhdev.com\/2013\/01\/tmux-tmuxifier-console-screen.html](http://blog.lyhdev.com/2013/01/tmux-tmuxifier-console-screen.html)

[http:\/\/blog.csdn.net\/gatieme\/article\/details\/49301037](http://blog.csdn.net/gatieme/article/details/49301037)

[https:\/\/linux.cn\/article-3952-1.html](https://linux.cn/article-3952-1.html)

[http:\/\/www.maxincai.com\/post\/tmux\/](http://www.maxincai.com/post/tmux/)

以後再玩config
[https:\/\/github.com\/gpakosz\/.tmux](https://github.com/gpakosz/.tmux)

###### 使用tmuxifier

```
# 在tmux window中...

# 新建
tmuxifier new-window project_name
# 修改設定
tmuxifier edit-window project_name
# 進入tmux後，可以讀此設定
tmuxifier load-window project_name

# 預設會存放在~/.tmuxifier/layouts下

```

