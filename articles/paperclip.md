把原本在public中的圖轉移至S3圖庫時沒有什麽問題，但在轉換為自訂的domain name時，發生許多問題，解法和問題記錄在此。（我們使用Paperclip作為圖片上傳工具）

# 資料轉移

在Gemfile加上`gem 'aws-sdk', '< 2.0'`，可以試著不用限定版本，不過我在上傳時有碰到問題，解法就是限制降版。

首先，public/


```
# has_attached_file :attachment, :styles => { :thumb => "100x100#", :small => "270x200#", :medium => "500x375#", :large => "800x450#"}, :default_url => "/images/products/:style/" has_attached_file :attachment, :styles => { :thumb => "100x100#", :small => "270x200#", :medium => "500x375#", :large => "800x450#"}, :url => ":s3_alias_url", :s3_host_alias => "s3-ap.dingtaxi.com", :path => "/:class/:attachment/:id_partition/:style/:filename"
```



[Paperclip 圖片上傳與額外說明](http://railsfun.tw/t/paperclip/64)




http://blog.project-sierra.de/archives/1701
https://www.stormconsultancy.co.uk/blog/development/ror-using-paperclip-with-amazon-s3-and-cloudfront/
http://stackoverflow.com/questions/29756313/custom-url-with-paperclip-and-aws-s3
https://github.com/thoughtbot/paperclip/blob/master/lib/paperclip/storage/s3.rb