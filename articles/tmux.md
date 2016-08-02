[Using zsh and vim to Improve Terminal Visibility](http://mayurrokade.com/blog/using-zsh-and-vim-to-improve-terminal-visibility/)



# zsh



# vim



# tmux

```
以下所有指令都在ctrl + b之後進行

tmux可以開啟server
一個server有許多session
一個session有許多視窗
一個視窗有許多區域

? 看說明 (M 代表要先按一下 Esc 鍵)
t 時鐘, 現在時間
: 指令模式
tmux ls 查目前session


Session
d 脫離目前session；暫時回到Shell界面，输入tmux attach能够重新进入之前的会话
D 選擇要脫離的session；若同時有多個sessions時使用
ctrl+z 關閉目前session
s 切換session；

****
多視窗操作
c：建立新的視窗；
d：卸載目前的 client；
l：與先前選擇的視窗間切換；
n：移動到下個視窗；
p：移動到上個視窗；
,：重新命名目前的視窗；
q：顯示各分割區塊的號碼（用來切換到不同的區塊）
o：切換到下個區塊；
w：列出目前 clinet 的視窗，用數字鍵選視窗
1：跳到指定的數字視窗


單一視窗多區塊之操作
%：當前區塊垂直切割成兩區塊；
"：當前區塊水平切割成兩區塊
:split-window：水平分離視窗；
o：移往下一個區塊；
q：顯示區塊的數字代號，當數字顯示時使用數字鍵移往該區塊；
上下左右 跳到上下左右的分割視窗
ctrl按著 + 上下左右, 可以調整視窗大小
{：將目前的區塊移往前一塊移動；
}：將目前的區塊移往後一塊移動；

分區大小快速重排（假設已經切了四個分割區域）
alt 1 直的切割視窗排列
alt 2 橫的切割視窗排列
alt 3 上面一個大的橫視窗 + 下面直的三個直視窗
alt 4 左面一個大的橫視窗 + 右面直的三個直視窗
alt 5 四個視窗各 1/4

```

Cheet sheet
https://gist.github.com/andreyvit/2921703


http://blog.chh.tw/posts/tmux-terminal-multiplexer/
http://blog.lyhdev.com/2013/01/tmux-tmuxifier-console-screen.html

http://blog.csdn.net/gatieme/article/details/49301037

https://linux.cn/article-3952-1.html

http://www.maxincai.com/post/tmux/


以後再玩config
https://github.com/gpakosz/.tmux


使用tmuxifier
`tmuxifier new-window example`
```


```


