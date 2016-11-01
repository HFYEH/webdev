因為實在少一台電腦螢幕，所以要玩RPi都得用電視。想想其實大多數時候我用不到螢幕，那就用ssh登入吧！

這篇文章說明如何用個人電腦登入Raspberry Pi的文字介面。當RPi已經安裝完成，且連接上網路後，就可以開始了。步驟不多，簡單記錄下來，順便給隊員參考。

### RPi連接網路
首先，確定要用來登入的電腦與RPi在同一個網域下。

進入RPi後打開terminal `ctrl + alt + t`，輸入`ifconfig`，找wlan0中的inet addr這行，會找到192.168.xxx.xxx，這是AP給這台RPi的內網IP，先記錄下來。

### 新增使用者
雖然Raspbian預設就有一個root帳號，但習慣上還是會開一個自己的帳號。一樣在terminal打
```
# 新增sharefun為新的使用者，根據提示輸入密碼和確認密碼後即完成
sudo adduser sharefun
# 取回管理員權限，如果已有管理者權限可跳過
sudo su
# 賦與sharefun管理者權限
adduser sharefun sudo
# 切換到sharefun
sudo su sharefun
# 上述指令會幫你將工作目錄一起建好，打cd即可切到工作目錄
```

### 下載putty
[下載putty](http://ntu.csie.org/~piaip/pietty/)，一個ssh連線的工具。

不用安裝，即可打開。

### 登入RPi
打開putty後，在session頁面，Host Name (or IP address)處輸入剛剛記下的IP，Connection Type選擇ssh，再按Open鈕。

Terminal會打開，叫你輸入帳號密碼，把剛剛做好的的帳號密碼打進去，或者使用RPi預設的帳號密碼就可以登入了！

### 解決 ssh 進RPi會lag的問題
```
# /etc/ssh/sshd_config 最後加入
UseDNS no
# 儲存後重啟ssh
sudo service ssh reload
```
參考
http://raspberrypi.stackexchange.com/questions/15568/raspberry-pi-ssh-login-slow
