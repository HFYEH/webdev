# 生成專案
`rails new orm_test --database=mysql`
建database
```
mysql -u root -p
> create database database_name;
> exit
```
database.yml輸入相應的database及密碼
移除不必要的gem
bundle install

# 產生垃圾資料

##### 生成migration檔
`rails g migration item`
In 20150622043409_item.rb
```
class Item < ActiveRecord::Migration
  def change
    create_table :items do |t|
      t.string :name
      t.integer :kind
      t.integer :parent_id
      t.timestamp :created_at
    end
  end
end
```

##### 進入rails console,自建model
`class Item < ActiveRecord::Base; end`

##### 產生亂數資料
```
offset = 60 * 60 * 24 * 365 * 2		# 兩年的秒數
now = Time.now			# 現在時間
now - offset				# 兩年間的總秒數(時間的運算是用整數運算的)

2000.times do |i|
	Item.create(name: rand, kind: rand(40), created_at: now-rand(offest))
end

item_count = Item.count
=> 2000
```

##### 幫垃圾資料加上隨機的老爸
```
Item.all.each do |item|
	if rand > 0.6
  	item.parent_id = rand(2000)
  end
  item.save
end
```


# 起手式

##### 跟資料庫溝通
`rails dbconsole` or `mysql -u root -p`
`use database_name;`		# 使用database
`show tables;`					# 看所有table

##### 起點
`SELECT * FROM items as i;`
`SELECT`
`*` 								//最後才看,所以先打*
`FROM items`				//接著是從哪張表撈資料出來
`AS i`							//sql下所有東西(包括talbe, column...)都可以被rename,使用as來命名

***sql在做join時很重視過濾,在此要決定是否要過濾,通常第一關會去掉很多不必要的資料,一關刪完後再join,再刪一關再join***

##### 第一關過濾 WHERE
針對第一個table設條件
`SELECT * FROM items AS i WHERE i.id>100;`

##### 第N關過濾 INNER JOIN
`SELECT i.id AS i_id, j.id AS j_id FROM items AS i INNER JOIN items AS j ON j.id>100 AND i.id=j.id WHERE i.id>100;`
WHERE是用來過濾第一個table
INNER JOIN			***兩邊組合起來時同時存在才有效(交集),INNER JOIN (內部連接) 為等值連接，必需指定等值連接的條件，而查詢結果只會返回符合連接條件的資料***

##### 補述句 LIMIT
`SELECT i.id AS i_id, j.id AS j_id FROM items AS i INNER JOIN items AS j ON j.id>100 AND i.id=j.id WHERE i.id>100 LIMIT 10;`

# Subquery
***subqeury吐出來的東西就是table,只要有table的地方就可以置換為subquery***

##### 不需要join時可以用subquery

##### 先測試將要作為subquery的query
`SELECT id FROM items WHERE id BETWEEN 10 AND 20;`
加框框完成subquery
`(SELECT id FROM items WHERE id BETWEEN 10 AND 20);`

##### 使用
`SELECT id FROM items WHERE id IN (subquery);`
`SELECT id FROM items WHERE id IN (SELECT id FROM items WHERE id BETWEEN 10 AND 20);`

***subqeury吐出來的東西就是table,只要有table的地方就可以置換為subquery***
`SELECT * FROM (SELECT id FROM items AS i WHERE id > 10) AS i INNER JOIN (SELECT id FROM items AS i WHERE id > 10) AS j ON i.id = j.id LIMIT 10;`

# GROUP BY
此為sql真正強悍之處，真正高手從這邊才開始
`SELECT * FROM items LIMIT 20;`
試著用kind作分類，並且要計算有多少個在同一個kind中
`SELECT id, kind, COUNT(kind), SUM(id), AVG(id) FROM items GROUP BY kind LIMIT 3;`

`SELECT id, kind, COUNT(kind), SUM(id), AVG(id)`	//拉出id(無意義),kind,kind計數,id的累加,id的平均
`FROM items`
`GROUP BY kind LIMIT 3;`		//使用kind欄位分類

# HAVING
二次的WHERE，已經取出東西後，若要再過濾一次，可以用subquery或用HAVING，但通常用到HAVING表示前面sql沒寫好...
`SELECT id, kind, COUNT(kind), SUM(id), AVG(id) FROM items GROUP BY kind HAVING kind > 1 LIMIT 3;`

# 出月報表、年報表

##### 先建subquery
`SELECT DATE_FORMAT(created_at, '%Y-%m-01') AS time, SUM(kind) AS kind FROM items GROUP BY DATE_FORMAT(created_at, '%Y-%m-01');`
使用DATE_FORMAT把資料改成以月為單位來GROUP

##### INSERT製作報表
`INSERT INTO items (name, kind, parent_id, created_at) SELECT name, kind, parent_id, created_at FROM items LIMIT 10;`


# 遠端備份
pg_dump -U username -h remote_db -p 5432 db_name > backup_name