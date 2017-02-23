# Build your own code push server on AWS EC2 and RDS

Reference: [code-push-server](https://github.com/lisong/code-push-server)

## Install

### Install lastest nodeJS

```
curl -sL https://deb.nodesource.com/setup_6.x -o nodesource_setup.sh

sudo bash nodesource_setup.sh

sudo apt-get install nodejs
```

### Install pm2 for node application production server
```
npm install pm2 -g
```

### Download code-push-server source code

```
# To your apps directory
git clone https://github.com/lisong/code-push-server.git
```

## Server setup

### Domain name, S3, RDS

...skip... Just remember the following key items, we will use in process.json later

S3 access: access_key_id, secret_access_key, bucket_name, region, url
RDS: rds_host, rds_username, rds_password

By the time this doc is wrote, the app can only read AWS credentials from environment
```
# ~/.bashrc
export AWS_ACCESS_KEY_ID=xxx
export AWS_SECRET_ACCESS_KEY=xxx
```

### Setting up NignX

```
# /etc/nginx/sites-enabled/your_domain_name
server {                                                                                                                                                            
  listen 80;
  listen [::]:80;
  server_name your_domain_name;

  location / {
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_set_header Host $http_host;
    proxy_pass http://127.0.0.1:3000;  # Depend on which port your app listen, we will use 3000
  }
}

# /etc/nginx/nginx.conf
include /etc/nginx/sites-enabled/*   # use settings inside sites-enabled folder
```

## Setting up app

I clone the project and tends not to modify it, so I simply add a process.json to set the config the app need.

Since the source code already provided a process.json, we can just copy it.
```
# Outside code-push-server directory
cp code-push-server/docs/process.json .

# process.json
{
  "apps" : [{
    "name"      : "code-push-server",
    "max_memory_restart" : "300M",
    "script"    : "./code-push-server/bin/www",
    "instances"  : "max", //开启实例数量，max为cpu核数
    "exec_mode"  : "cluster", //集群模式，最大提升网站并发
    "env" : {
      "NODE_ENV" : "production",
      "PORT" : 3000,     // 指定 port
      "CONFIG_FILE" : "./code-push-server/config/config.js",  // 當前檔案的設定會進入config.js
      "RDS_USERNAME": "codepush",
      "RDS_PASSWORD": "rds_password",
      "RDS_HOST": "rds_host_url",
      "BUCKET_NAME": "s3_bucket_name",                                                                                                             
      "REGION": "ap-northeast-1",
      "DOWNLOAD_URL": "http://codepush.dingtaxi.com.s3-ap-northeast-1.amazonaws.com",  // 有兩種形式，一是"resion/bucket_name"，另一種是"bucket_name.region"
      "DATA_DIR": "/home/ubuntu/apps/code-push-server/data",
      "STORAGE_TYPE": "s3"   // 此項要設定，否則預設會讀 local 作為 bundle file 存放處
    }
  }]
}

# Init database, go to app folder
bin/db init --dbhost rds_host_url --dbuser rds_user_name --dbpassword rds_password
# If your database already build, you may face error. Simple comment out relevant code in bin/db or delete the db then run this command again
# This will give you default admin user, with username/password admin/123456
```

## Manage and maintenance server
```
bin/www                    # Development mode run server (in app folder)
pm2 start process.json     # Start app using configs in process.json
pm2 status                 # View current app status
pm2 stop your_app_name     # Stop app
pm2 restart your_app_name  # Restart app
pm2 delete your_app_name   # Delete your app from pm2 status list (would not delete app source code itself)
# If you cannot login, you might have to delete the app completely, making sure db is OK, then start it again
# If you code-push failed, it could be AWS credential not read, also delete the app and start it again

# Update code
git pull --rebase origin master
bin/db upgrade --dbhost rds_host_url --dbuser rds_user_name --dbpassword rds_password
```

## Local usage

### Change code-push deployment key

Read the doc, edit deployment key and url in your mobile app MainActivity.java, Info.plist.

## login codepush service

```
code-push logout
code-push login your_domain_name
```

After enter the token, you can use code-push command as usual.