# Using Array in Grape

使用 Grape 時，如果帶參數的某個欄位要放 array，有以下幾種做法

1. 欄位 type 設定為 json，最常見的方式

```
...
params do
  requires :abc, type: JSON, documentation: { default: "{\"data\":[\"good\",\"food\"]}" }
end
...
```

要注意的是，如果要在 Swagger doc 顯示預設值，需在 documentation 打出 serialized json，套版時才能正確帶入預設值。

且這個欄位不可以放 Array，一定要有 key，否則會直接回傳格式不符合 json，這應該是套件限制一定要帶 key-value 的 json 了。


2. 使用 Rails 的慣例

對於 application/x-www-form-urlencoded，存在 query param 後蓋前的問題，如果是 `abc=xxx&abc=ooo`，最後只會解析為 `params[:abc] == "ooo"`，Rails 預設是可以吃 `abc[]=xxx&abc[]=ooo`，可以解析為 `params[:abc] == ["xxx", "ooo"]`。

```
...
params do
  optional :"abc[]", type: Array[String]
end
...
```

但這就限制 key一定要掛一個中括號，而且前端也會很困惑，同樣的key要帶兩次，值又不同。

當然，如果硬要用不帶中括號的方法，仍然是可以的，由我們重新解析一次即可。

```
...
params do
  optional :abc, type: Array[String]
end
post do
  if params[:abc].present?
    params[:abc] = CGI.parse(request.body.string)["tags"]
  end
end
...
```

對於 multipart/form-data

```
...
params do
  optional :abc, type: Array[String], coerce_with: ->(val) { CGI.parse(val)["tags"] }
end
...
```
