

[Everything you know about html_safe is wrong](https://makandracards.com/makandra/2579-everything-you-know-about-html_safe-is-wrong)

Rails render template時，會將字串轉為ActiveSupport::SafeBuffer類別的實例。轉換過程中，會escape html的`<`和`>`。預設會將所有的字串都轉為ActiveSupporr::SafeBuffer才會render。

html_safe功能與raw一樣，用意在轉換為ActiveSupport::SafeBuffer的過程中***避免 html escape***，只有兩處差別

1. raw是一個helper，只能用在controller和view
2. html_safe無法對nil做，因為nil不具有此方法

如果把String實例和ActiveSupport::SafeBuffer實例相加，會回傳新的ActiveSupport::SafeBuffer實例，所以除非把不想要escape的部份逐一手動處理，不然一定會在轉換過程中變為html escape。

一個常見的錯誤是在helper中將整段包含使用者輸入的內容使用html_safe。以下情況應該用`content_tag`處理。

```ruby
content = "alert('!!!');"

def render_div(content)
  "<div>#{content}</div>".html_safe
end

#=> <div><script>alert("!!!");</script></div>
```

切記，永遠不要對使用者提供的內容做html_safe，可永保平安。