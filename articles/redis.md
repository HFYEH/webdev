
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


