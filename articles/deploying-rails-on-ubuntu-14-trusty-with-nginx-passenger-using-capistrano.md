# Part 1 虛擬環境設置
在這裡小卡關，因為機器沒有開啟Visualization，又因為一開始沒有在Vagrantfile裡打開virtualbox視窗的選項，所以看到錯誤訊息一直不明究理。總之，到BIOS設定開啟visualization後就好了。

[參考資料](http://www.codedata.com.tw/social-coding/vagrant-tutorial-5-vm-customization/)

使用Vagrant + VirtualBox，使用ubuntu-trusty-64鏡像檔開啟後
`vagrant ssh`登入（預設會使用vagrant/vagrant登入）
因為user和ssh已經被Vagrant設定好了，這邊先跳過

之前用慣VirtaulBox的圖形介面，把Vagrant CLI與其相對應一下  
`vagrant init` = 從image開新的vm  
`vagrant up` = 開啟該vm  
`vagrant halt` = 把該vm關機  
`vagrant destroy` = 從VirtualBox移除該vm（image並不會移除）  
`vagrant box list` = 看有那些image(box)  
`vagrant box remove _box_name_` = 移除特定image(box)

[Vagrant基礎說明](http://www.codedata.com.tw/social-coding/vagrant-tutorial-4-guest-host-communication/)

[Vagrant多機器](http://gogojimmy.net/2013/05/26/vagrant-tutorial/)

[Vagrant Official site](https://www.vagrantup.com/docs/cli/)

[Vagrant + Docker](http://samchu.logdown.com/posts/288889-docker-fast-with-vagrant-and-construct-development-and-test-environments)


# Part 2 更新
`sudo apt-get update`

`sudo apt-get upgrade`

# Part 3 新增使用者 deploy

`adduser --disabled-password deploy`

`adduser deploy sudo` # add user deploy to sudo group

`sudo su deploy` # switch to deploy

# Part 4 ssh 登入

沒有公私鑰才使用下列指令，不然會蓋掉原本的

`ssh-keygen -t rsa` or `ssh-keygen -t rsa -C "<your email address>"`

複製本機的 ~/.ssh/id_rsa.pub 到機器的/home/deploy/.ssh/authorized_keys

`cat ~/.ssh/id_rsa.pub | ssh deploy@xxx.xxx.xxx.xxx 'cat >> ~/.ssh/authorized_keys'`

`chmod 640 /home/deploy/.ssh/authorized_keys`

`chmod 700 /home/deploy/.ssh`  owner可以執行和讀寫

`chown deploy:deploy /home/deploy -R`

讓deploy以sudo執行指令時不須密碼

```
sudo visudo  進入編輯

...
# User privilege specification
#root   ALL=(ALL:ALL) ALL
root    ALL=(ALL) NOPASSWD: ALL
deploy  ALL=(ALL) NOPASSWD: ALL

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
%sudo   ALL=(ALL) NOPASSWD: ALL
...
```

參考[My First 10 Minutes On a Server - Primer for Securing Ubuntu](https://www.codelitt.com/blog/my-first-10-minutes-on-a-server-primer-for-securing-ubuntu/)的安全設定，此篇極有幫助

更改ssh登錄port，並拒絕密碼登入，在server的修改(sshd_config是作為server端的設定，ssh_config是作為client端的設定)
sudo vi /etc/ssh/sshd_config
```
PermitRootLogin no         # 注意，設定後即不能以root登入了
PasswordAuthentication no  # 不能以密碼驗證

TCPKeepAlive yes           # 設定讓server固定送alive訊息到client
ClientAliveInterval 30     # 送出訊息的間隔，預設0是不送
ClientAliveCountMax 3      # 送出幾次沒回應之後就中斷連線
```
執行`sudo service ssh restart`使之生效

如果之後搞壞.bashrc，導致登不進去（我就發生這個問題），可以在不跑.bashrc的情況登入（因為已經不能用密碼登入了）  

`ssh -t username@xxx.xxx.xxx.xxx /bin/sh`

# Part 5 裝機

- 先裝git `sudo apt-get install git`
- 調整時區 `sudo dpkg-reconfigure tzdata`，並以 `date` 檢查是否正確設定
- 安裝常用套件，以實戰聖經為基礎，新增一些常用的套件，包含nodejs, imagemagick, redis

```
// Build tool and library for ruby
sudo apt-get install build-essential zlib1g zlib1g-dev libssl-dev libreadline6-dev libyaml-dev git-core bison openssl curl libsqlite3-0 libsqlite3-dev sqlite3 autoconf libc6-dev libpcre3-dev curl libcurl4-nss-dev libxml2-dev libxslt-dev libffi-dev redis-server imagemagick nodejs ruby-dev liblzma-dev libgmp-dev

// 下載並安裝NodeJS
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs

sudo apt-get install vim

// Install rvm and Ruby
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

\curl -sSL https://get.rvm.io | bash -s stable

// Ubuntu已可以透過套件管理系統安裝RVM
sudo apt-get install software-properties-common

sudo apt-add-repository -y ppa:rael-gc/rvm
sudo apt-get update
sudo apt-get install rvm

rvm install 2.3.0
rvm --default use 2.3.0
gem install bunlder --no-doc --no-ri
```

- 裝postgres

因為[語系設定問題](http://dba.stackexchange.com/questions/50906/why-wont-postgresql-9-3-start-on-ubuntu
)，會導致postgres無法安裝，先設定好可免除後續debug。

`sudo vi /etc/default/locale`加入三行
```
LANG="en_US.UTF-8"
LANGAUGE="en_US.UTF-8"
LC_ALL="en_US.UTF-8"
```
再執行`sudo dpkg-reconfigure locales` 即可

正式安裝[postgresql](/articles/postgresql-on-ubuntu.md)

`sudo apt-get install postgresql libpq-dev postgresql-contrib postgresql-client`

以postgres登入db server，創建使用者
```
// 切換成postgres
sudo su postgres

// 登入db server
psql

CREATE ROLE deploy SUPERUSER LOGIN;
ALTER USER deploy WITH PASSWORD 'your_password';
```
 
- 裝Nginx + Passenger

[Install Passenger + Nginx with APT](https://www.phusionpassenger.com/library/install/nginx/install/oss/)

如果需要移除系統預裝的nginx

`sudo apt-get remove nginx nginx-common` # Removes all but config files.

`sudo apt-get purge nginx nginx-common` # Removes everything.

`sudo apt-get autoremove` # After using any of the above commands, use this in order to remove dependencies used by nginx which are no longer required.

`sudo apt-get purge nginx nginx-full nginx-light nginx-naxsi nginx-common`

碰到虛擬記憶體不足的問題，依建議輸入指令即可解決

驗證安裝是否成功

`sudo /usr/bin/passenger-config validate-install`

檢查記憶體使用狀態

`sudo /usr/sbin/passenger-memory-stats`

片尾有訊息說要改passenger和ruby的路徑，可用下列指令查詢，並在其下找passenger path

`which ruby`

`which passenger`

nginx 調整見這篇[]

nginx 操作
```
sudo service nginx start
sudo service nginx stop
sudo service nginx restart
sudo service nginx configtest              // 設定檔路徑和格式檢查
sudo nginx -t                              // 同上
netstat -tlpn                              // check netstat binding
sudo netstat -tulpn | grep --color :80     // 同上，順便印出PID
```

# Part 6 用 Capistrano 部署 rails app
Capistrano在本機運行，它的工作是登入你的伺服器，進行一系列shell操作。
首先先在本地端安裝capistrano gem
`gem install capistrano`
然後在Gemfile中加上（實際上依applicatoin server的不同，下面會有所不同）

`bin/rake db:environment:set RAILS_ENV=production`
```
gem 'capistrano-rails', :group => :development
gem 'capistrano-passenger', :group => :development
```
在專案下執行初始化capistrano
`cap install`   會產生Capfile（基礎設施宣告，宣告會用到的外掛和自訂任務）, config/deploy.rb（全域變數設定檔，會被所有stage引用）, config/deploy/production.rb（不同的部署環境）, config/deploy/staging.rb（不同的部署環境）

基本上，Capfile裡面就有說明文檔的連結，可以知道怎麽設定deploy.rb。看[這篇](http://www.slideshare.net/zx1986/deployment-with-capistrano-59855817)了解這些生出來的檔案都寫了些什麽

做 deploy 前的檢查，並把遠端 (production) 需要的資料夾建立，如果有 error 請檢查錯誤訊息去 debug
`cap production deploy:check`

假裝跑看看
`cap production deploy --dry-run`

把secret.yml, database.yml複製一份到server，並且使用`rake secret`得到本地端的secret_key_base，貼至server。
gitignore上述兩個檔案。

執行 deploy 程序正式運作自動化 deploy
`cap production deploy`

得知passenger_ruby的位置
`passenger-config about ruby-command`

測試格式是否正確
`sudo nginx -t`

檢查nginx狀態
`sudo service nginx status`

bundle exec passenger start
bundle exec passenger restart

passenger-config validate-install

passenger-memory-stats

passenger-status

passenger-config --root  


If you want to restart using `touch tmp/restart.txt`, add this to your config/deploy.rb:
`set :passenger_restart_with_touch, true`

在第二台機器部署時，跑passenger錯誤：
```
/home/deploy/.rvm/rubies/ruby-2.3.0/lib/ruby/2.3.0/rubygems/specification.rb:2158:in `method_missing': undefined method `this' for #<Gem::Specification:0xcbfb74 passenger-5.0.27> (NoMethodError)
```
`gem -v` 可以看目前版本
是因為rubygem在2.5.1有問題，所以降版改為2.4.8
`gem update --system 2.4.8 --no-ri --no-rdoc`
[ref](http://stackoverflow.com/questions/35549385/rails-error-method-missing-undefined-method-this-for)


# Part 7 第一次部署
bundle exec rake db:create RAILS_ENV=production
bundle exec rake db:migrate RAILS_ENV=production
RAILS_ENV=production passenger start


# Part 8 轉址常用指令
在`vi /etc/hosts`中設定

```
xxx.xxx.xxx.xxx  your_name_server
```

在console可以測試其對應

```
host your_name_server
dig your_name_server
ping your_name_server
nslookup your_name_server
```

# Part 9 SSL 設置
[Setting up a SSL Cert from Comodo](https://gist.github.com/bradmontgomery/6487319)


### Letsencrpyt

在public先新增資料夾.well-known/acme-challenge/

`sudo certbot certonly --webroot -w /home/deploy/apps/mappa_caffe/current/public/ -d map-aroma.com -d www.map-aroma.com`

# Part 10 nginx 設置

移至[nginx](nginx.md)

# Part 11 Advanced security setup

## iptables using uncomplicated firewall (ufw)

查詢當前的網路服務

`sudo netstat -plunt`

查詢當前的設定

`sudo iptables -L -n`

ufw是一套iptables的前端設定，後面走的還是iptables，見[Ubuntu 的 ufw 和 iptables 有關係嗎?](http://www.arthurtoday.com/2015/01/different-between-ufw-and-iptables.html)

ufw語法較單純，容易設定，iptables則可以實現較進階的內容，見[iptables 設定入門](http://s2.naes.tn.edu.tw/~kv/iptables.htm)

照順序執行，別把自己反鎖了

```
sudo ufw version        # 檢查版本
sudo ufw status         # 查看狀態，預設是Status: inactive(表示狀態為閒置)
sudo ufw disable        # 關閉防火牆，會變成上述的閒置狀態
sudo ufw default deny   # 拒絕所有incoming封包，最後不加incoming/outgoing的話，預設為incoming

# 設定port
sudo ufw <allow/deny> <port>
sudo ufw allow smtp     # 允許所有的外部IP訪問本機的25/tcp (smtp)端口(信箱用)
sudo ufw allow ssh      # 允許所有的外部IP訪問本機的ssh端口(終端機用)這很重要
sudo ufw allow 53       # 允許外部訪問53端口(tcp/udp) (DNS訪問)這很重要
sudo ufw allow 80       # 允許外部訪問80端口(tcp/udp) 網頁用
sudo ufw allow http     # 同上，ufw知道http用80
sudo ufw allow 443      # 允許外部訪問443端口(tcp/udp)網頁用
sudo ufw enable         # 打開防火牆，會變成Status: active
sudo ufw status         # 看目前狀態

sudo ufw <allow/deny> <port/tcp_or_udp>   # 進一步指定規則為tcp或udp連線
sudo ufw <allow/deny> <in/out> <port>     # 預設是設定連入，要設定連出則加out
sudo ufw delete <allow/deny> <port>       # 加delete就是刪除規則

# 設定ip
sudo ufw <allow/deny> <from/to> <ip> port <port>

sudo ufw allow from 10.0.0.0/8
sudo ufw allow from 172.16.0.0/12
sudo ufw allow from 192.168.0.0/16   # 以上三行允許所有來自區網網路使用

sudo ufw allow from 127.0.0.1 to any port 3389    # 允許來自 127.0.0.1 (本機) 的 3389 埠口連線 (xrdp)(用於遠端連線)
sudo ufw allow from 192.168.1.100   # 允許此IP訪問所有的本機端口(非必要)
```

參考自[Ubuntu based GNU/Linux 上的防火牆 (ufw) 基本設定](https://www.peterdavehello.org/2016/01/ubuntu-based-gnulinux-firewall-ufw-essential-config/)

## Fail2ban

[fail2ban： 新手老手 root 網管都要練的金鐘罩](https://newtoypia.blogspot.tw/2016/04/fail2ban.html)

[用 Fail2Ban 防範暴力破解 (SSH、vsftp、dovecot、sendmail)](http://www.vixual.net/blog/archives/252)

新增/etc/fail2ban/jail.local

```
[sshd]
enabled  = true
port     = ssh
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 5
findtime = 600
bantime  = 1200
```

`sudo service fail2ban start`

`sudo fail2ban-client status`

`sudo fail2ban-client status sshd`

## Monit

`sudo apt-get install monit`

編輯 monitrc 打開 set httpd 的那四行

輸入 sudo service monit restart 重啟

輸入 sudo monit status 可以看到應該有成功在監測 sidekiq


## 設定logrotate


## 設定Crontab