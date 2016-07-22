把原本在public中的圖轉移至S3圖庫時，發生許多問題，解法和問題記錄在此

我們使用Paperclip作為圖片上傳工具。

# 資料轉移

如果使用

首先，public/

```
# has_attached_file :attachment, :styles => { :thumb => "100x100#", :small => "270x200#", :medium => "500x375#", :large => "800x450#"}, :default_url => "/images/products/:style/" has_attached_file :attachment, :styles => { :thumb => "100x100#", :small => "270x200#", :medium => "500x375#", :large => "800x450#"}, :url => ":s3_alias_url", :s3_host_alias => "s3-ap.dingtaxi.com", :path => "/:class/:attachment/:id_partition/:style/:filename"
```


