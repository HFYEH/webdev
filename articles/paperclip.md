## Paperclip使用S3，並使用自訂的 Domain Name


### 先將paperclip預設的檔案路徑/system下的所有檔案直接搬到S3

1. 建立bucket，記得bucket name
2. 修改bucket policy，開放一般人的讀取權限
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::<bucket_name>/*",
            ]
        }
    ]
}
```
3. 在Web版介面直接上傳整個資料夾至bucket中（去掉原本的/system）

### Rails裡修改設定

In application.rb
```
    ...
    config.paperclip_defaults = {
      storage: :s3,
      s3_region: 'ap-northeast-1',                        // 以東京機房為例
      s3_protocol: 'http',                                // http or https
      s3_host_alias: <your_alias_host_name>,              // 顯示給用戶的網址的host，會覆蓋下方的s3_host_name設定
      s3_credentials: {
        // 以下四項必填
        bucket: <your_bucket_name>,                       // 剛剛創建的bucket名稱
        access_key_id: <your_access_key_id>,              // 此項與下一項是AWS給你的
        secret_access_key: <your_secret_access_key>,
        s3_host_name: 's3-ap-northeast-1.amazonaws.com'   // 以東京機房為例
      }
    }
    ...
```

In attachment model
```
class Poi::Image < ResourceBlock
  # 原本
  has_attached_file :attachment, {
    :styles => {
      :thumb => "100x100#",
      :small => "270x200#",
      :medium => "500x375#"
    },
    :default_url => "/images/<model_name>/:style/"
  }

  # 後來
  has_attached_file :attachment, {
    :styles => {
      :thumb => "100x100#",
      :small => "270x200#",
      :medium => "500x375#"
    },
    :url => ":s3_alias_url",
    :s3_protocol => ...,                                 // 此例中將會讀到'http'
    :s3_host_alias => ...,                               // 此例中將會讀到<your_alias_host_name>
    :path => "/:class/:attachment/:id_partition/:style/:filename"
  }
end
```


## Url解釋

在繼續說明問題前，先看我們碰到什麽問題

```
// S3上顯示的圖片的路徑是這樣
http://s3-ap-northeast-1.amazonaws.com/s3-ap.dingtaxi.com/product/images/attachments/000/001/031/small/Chiang_Mai_Old_City.jpg


// 應該是代表
https://:s3_host_name                  /:s3_bucket_name   /:model_name   /attachment /:id_partition/:style/image_file_name.jpg

// 但是可以發現，即使是換成下面這個也可以正確下載，顯示:s3_host_name/:s3_bucket_name == :s3_bucket_name.:s3_host_name 的事實
http://s3-ap.dingtaxi.com.s3-ap-northeast-1.amazonaws.com/product/images/attachments/000/001/031/small/Chiang_Mai_Old_City.jpg

// 雖然S3已經設定成接受跨站請求，但是在手機上時，全站是https，手機會擋下不是https的請求，所以要上https，上面的:s3_protocol在產品上要改成'https'
// 這造成另一個問題，因為S3實際上是沒有https的，所以瀏覽器會擋下未經驗證的https，警告使用者，又因為請求是圖片，所以警告不會跳出來...
// 所以才設定一個假名，即:your_alias_host_name，這裡設定的就是網站原本的host name
// 但該圖已經被搬到S3了，所以我們要再NginX設定foward proxy

```


## NginX設定

```
http {
    server_name <your_host_name>
    server {
        location ^~ /<model_name>/ {
            proxy_pass http://s3-ap.dingtaxi.com.s3-ap-northeast-1.amazonaws.com/<model_name>/;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}

```


### 參考連結：

[Paperclip 圖片上傳與額外說明](http://railsfun.tw/t/paperclip/64)

[Paperclip doc about S3](http://www.rubydoc.info/gems/paperclip/Paperclip/Storage/S3)

[Stackoverflow](http://stackoverflow.com/questions/29756313/custom-url-with-paperclip-and-aws-s3)
