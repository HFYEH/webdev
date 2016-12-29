安裝必要lib和postgres
`sudo apt-get install postgresql libpq-dev postgresql-contrib postgresql-client`

安裝 pgadmin3 的客戶端管理工具（可跳過）
`sudo apt-get install pgadmin3`

切換到 postgres 帳號， postgres 預設沒有密碼且是 superuser 權限
`sudo su - postgres` \(to postgres directory\)
`sudo su postgres` \(to current directory\)

`psql` \(登入db\)

`\du` See all users
`\list or \l` list all databases
`\dt` list all tables in the current database
`\password postgres` 為postgres帳號新增密碼
`\q` ※退出

建立新使用者\(In postgres server\)

```
CREATE ROLE username SUPERUSER LOGIN;
or
CREATE USER username SUPERUSER;

ALTER USER username WITH PASSWORD '密碼';

ALTER ROLE ※看到這結果表示修改成功
```

```
# 事後改權限
ALTER ROLE username WITH Superuser;
```

# export database
```
pg_dump --host dingtaxi.ck44hqdryldr.ap-northeast-1.rds.amazonaws.com --port 5432 --username dingtaxi --dbname dingtaxi > $BACKUP_DIRECTORY/${1}_database_${CURRENT_DATE}.sql
```
# import database

psql databasename &lt; data\_base\_dump
[db權限問題](http://stackoverflow.com/questions/18664074/getting-error-peer-authentication-failed-for-user-postgres-when-trying-to-ge)

### Heroku db dump

`heroku pg:backups public-url b001 --app sushi`
`curl -o latest.dump`heroku pg:backups public-url`--app sushi`
sushi是app名稱

### Load to linux db

從heroku抓下來的db不能load，改用
`pg_restore latest.dump | psql db_name`
psql後面要接database name，否則會以user為database name

現在，我們就可以在資料庫伺服器上使用psql或者pgAdmin操作資料庫了。但是若想用第二項安裝的pgAdmin III管理工具，在啟動pgAdmin前需要建立修改一些PostgreSQL。打開命令列。首先，我们需要编辑postgresql.conf：
`sudo gedit /etc/postgresql/9.0/main/postgresql.conf`

```
#listen_addresses = ‘localhost’ => listen_addresses = ‘*’
#password_encryption = on => password_encryption = on
```

存檔並關閉gedit。

最後一步，修改pg\_hba.conf。
`sudo gedit /etc/postgresql/9.0/main/pg_hba.conf`
修改下面內容

```
# "local" is for Unix domain socket connections only
local all all   ident
# IPv4 local connections:
host all all 127.0.0.1/32 md5
# IPv6 local connections:
host all all ::1/128 md5
改成md5
更改成下面內容
# "local" is for Unix domain socket connections only
local all all                                md5
# IPv4 local connections:
host all all 127.0.0.1/32    md5
host all all 192.168.0.0/24 md5
# IPv6 local connections:
host all all ::1/128       md5
192.168.0.0要隨著主機IP位置改變，如主機IP是211.21.19.100，就改成 211.21.19.0
重啟伺服器即可。
```

現在可以在Ubuntu下使用PostgreSQL了。
`sudo /etc/init.d/postgresql restart`

建立使用者帳號sharefun，-r 能夠建立 role ，並且賦予  -s 超級使用者、-d 能夠建立 DB 、-l 能夠登入系統、-P能夠建立密碼
`createuser --r sharefun -s -d -l  -P`

建立資料庫  屬於 -O sharefun 使用者的
`createdb  -O sharefun my_database_development`
`createdb  -O sharefun my_database_test`
`createdb  -O sharefun my_database_production`



遠端操作資料庫

psql --host my_host --port 5432 --username my_user_name --dbname my_db_name -f output.sql

pg_dump ...

