[Using zsh and vim to Improve Terminal Visibility](http://mayurrokade.com/blog/using-zsh-and-vim-to-improve-terminal-visibility/)



# zsh



# vim



# tmux

```
以下所有指令都在ctrl + b之後進行

? 看說明 (M 代表要先按一下 Esc 鍵)
t 時鐘, 現在時間
q 可以看到視窗數字 - 若有切割視窗比較容易懂


切割視窗有些 Default Layout 可以用, 假設我已經切了四個分割視窗
Esc 1 直的切割視窗排列 (M-1)
Esc 2 橫的切割視窗排列 (M-2)
Esc 3 上面一個大的橫視窗 + 下面直的三個直視窗 (M-3)
Esc 4 左面一個大的橫視窗 + 右面直的三個直視窗 (M-4)
Esc 5 四個視窗各 1/4 (M-5)

c：建立新的視窗；
d：卸載目前的 client；
l：與先前選擇的視窗間切換；
n：移動到下個視窗；
p：移動到上個視窗；
,：重新命名目前的視窗；
q：顯示各分割區塊的號碼（用來切換到不同的區塊）
o：切換到下個區塊；
w：列出目前 clinet 的視窗，可以用數字鍵切換；


單一視窗多區塊之操作
%：當前區塊垂直分離成兩區塊；
:split-window：水平分離視窗；
o：移往下一個區塊；
q：顯示區塊的數字代號，當數字顯示時使用數字鍵移往該區塊；
上下左右 跳到上下左右的分割視窗
ctrl按著 + 上下左右, 可以調整視窗大小
{：將目前的區塊移往左邊；
}：將目前的區塊移往右邊；

```


http://blog.chh.tw/posts/tmux-terminal-multiplexer/


