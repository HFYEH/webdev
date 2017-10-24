
# Redis 使用紀錄

## 安裝 Redis Server

ubuntu

`sudo add-apt-repository ppa:chris-lea/redis-server`
`sudo apt-get update`
`sudo apt-get install redis-server`

mac

`brew install redis`

## 預設的 redis.conf 文件位置

`/etc/redis/redis.conf`
`/usr/local/etc/redis.conf`

## 啟動

`redis-server /path_to/redis.conf`

在mac下可以用

`brew services start redis`

## 設定開機時啟動

ubuntu

預設裝完就會設定完成

mac

設定自啟動
`launchctl load ~/Library/LaunchAgents/homebrew.mxcl.redis.plist`

取消自啟動
`launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.redis.plist`

## Benchmark

`redis-benchmark -q -n 100000`

## 安全

Redis是獨立的機器，我想只讓某些人連進來

```
sudo ufw disable
sudo ufw allow ssh                        # 所有人可以用ssh連線
sudo ufw allow from xxx.xxx.xxx.xxx to any port 6379  # 讓某ip可以連到6379
sudo ufw enable

sudo ufw status
```