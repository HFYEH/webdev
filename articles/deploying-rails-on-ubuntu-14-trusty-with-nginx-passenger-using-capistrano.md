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

（Vagrant基礎說明 http://www.codedata.com.tw/social-coding/vagrant-tutorial-4-guest-host-communication/）

# Part 2 更新
`sudo apt-get update`

`sudo apt-get upgrade`

# Part 3 新增使用者sharefun

`sudo adduser sharefun` or `sudo adduser --disabled-password sharefun`  
`sudo passwd sharefun`  
`sudo su` # retrieve root  
`adduser sharefun sudo` # make sharefun have sudo power  
`exit`  
`sudo su sharefun` # switch to sharefun  

# Part 4 ssh 登入
沒有公私鑰才使用下列指令

`ssh-keygen -t rsa`  

複製本機的 ~/.ssh/id_rsa.pub 到機器的/home/sharefun/.ssh/authorized_keys  

`cat ~/.ssh/id_rsa.pub | ssh sharefun@xxx.xxx.xxx.xxx 'cat >> ~/.ssh/authorized_keys'`

chmod 644 /home/sharefun/.ssh/authorized_keys  
chown sharefun:sharefun /home/sharefun/.ssh/authorized_keys  

更改ssh登錄port，並拒絕密碼登入，在server的修改  
sudo vi /etc/ssh/sshd_config
```
PasswordAuthentication no
```
執行`sudo service ssh restart`使之生效  

如果之後搞壞.bashrc，導致登不進去（我就發生這個問題），可以在不跑.bashrc的情況登入（因為已經不能用密碼登入了）  

`ssh -t username@xxx.xxx.xxx.xxx /bin/sh`

# Part 5 裝機

- 先裝git `sudo apt-get install git`
- 調整時區 `sudo dpkg-reconfigure tzdata`
- 安裝常用套件，以實戰聖經為基礎，新增一些常用的套件，包含nodejs, imagemagick, redis

```
// Build tool and library for ruby
sudo apt-get install build-essential zlib1g zlib1g-dev libssl-dev libreadline6-dev libyaml-dev git-core bison openssl curl libsqlite3-0 libsqlite3-dev sqlite3 autoconf libc6-dev libpcre3-dev curl libcurl4-nss-dev libxml2-dev libxslt-dev libffi-dev redis-server imagemagick nodejs ruby-dev liblzma-dev libgmp-dev

// 下載並安裝NodeJS
curl -sL https://deb.nodesource.com/setup_6.x -o nodesource_setup.sh

sudo bash nodesource_setup.sh

sudo apt-get install nodejs

sudo apt-get install vim

// Install rvm and Ruby
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable // 預設裝在使者目錄
\curl -sSL https://get.rvm.io | sudo bash -s stable // 裝在/usr/local/rvm

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
 
- 裝Nginx + Passenger（兩種方式）

[Install Passenger + Nginx with APT](https://www.phusionpassenger.com/library/install/nginx/install/oss/)）


[Install Passenger + Nginx with RVM](https://www.phusionpassenger.com/library/install/nginx/install/oss/rubygems_rvm/)



移除系統預裝的nginx
`sudo apt-get purge nginx nginx-full nginx-light nginx-naxsi nginx-common`

`sudo rm -rf /etc/nginx`

`gem install passenger --no-ri --no-rdoc`

`which passenger-install-nginx-module`

`rvmsudo 上面的輸出`

碰到虛擬記憶體不足的問題，依建議輸入指令即可解決  

驗證安裝是否成功  

`rvmsudo passenger-config validate-install`  

片尾有訊息說要改passenger和ruby的路徑，可用下列指令查詢，並在其下找passenger path  

`which ruby`  

`which passenger`  

如果無法啟動rails server，出現這樣的訊息  

The program 'rails' can be found in the following packages:
 * ruby-railties-3.2
 * ruby-railties-4.0
Try: sudo apt-get install <selected package>

在.bashrc加入
source ~/.profile
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"


接著我們設定 Nginx 啟動腳本：
```
git clone git://github.com/jnstq/rails-nginx-passenger-ubuntu.git
sudo mv rails-nginx-passenger-ubuntu/nginx/nginx /etc/init.d/nginx
sudo chown root:root /etc/init.d/nginx
```

nginx操作
```
sudo service nginx start
sudo service nginx stop
sudo service nginx restart
sudo service nginx configtest              // 設定檔路徑和格式檢查
sudo nginx -t                              // 同上
netstat -tlpn                              // check netstate binding
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

# nginx 設置

[DingTaxi設置](https://gist.github.com/HFYEH/914827ce9c31710b5853fa322b7bc08c)

# Production environment build up 其他參考資料

Ruby China
https://ruby-china.org/wiki/deployment-rails

Ruby China
https://ruby-china.org/topics/18616


# Vagrant 參考資料

Vagrant多機器
http://gogojimmy.net/2013/05/26/vagrant-tutorial/

Vagrant Official site
https://www.vagrantup.com/docs/cli/

Vagrant + Docker
http://samchu.logdown.com/posts/288889-docker-fast-with-vagrant-and-construct-development-and-test-environments
