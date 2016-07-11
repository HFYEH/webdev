---
layout: post
title: 'Grape 初體驗（於rails）'
date: 2016-04-29 06:19
comments: true
categories: 
---
第一次使用grape。看了JC大的[Ruby / Sinatra / Rails : Grape & Swagger](http://jokercatz.blogspot.tw/2015/05/ruby-sinatra-rails-grape-swagger.html)和[大亂鬥的日常：Rails Grape Swagger](https://www.youtube.com/watch?v=aJoD-d51_38)，搭配超複雜的github文檔，整理成這篇文章。

Grape是一個使用Ruby做的Restful API framework，可以單獨跑Rack或是與現有的app搭配一起使用。使用Grape創建的API，會在middleware層就打掉，不會有log，也比較快速。

首先安裝grape，在`Gemfile`裡
```ruby
gem 'grape'
gem 'grape-swagger
gem 'grape-swagger-rails'
```
可以只裝第一個，這篇的所有內容用第一個gem可以了。記得跑`bundle install`。

在`config/application.rb`上加上兩行，讓rails可以讀到等一下要寫的檔案。（預設只會讀rails new產生的那些檔案而已）等一下要寫的所有檔案將會放在app/api裡面。
```ruby
config.paths.add File.join('app', 'api'), glob: File.join('**', '*.rb')
config.autoload_paths += Dir[Rails.root.join('app', 'api', '*')]
```

在`routes.rb`加入
```ruby
mount GeneralApi::ApiV1 => '/api/v1'
```
mount關鍵字是告訴rails app說我們有另一個app在跑，在此例中，我們的app是一個API Rack app。
它會先跑middleware，如果沒找到path才會到Rails端，所以應該把api的routes都寫到routes的上方。
而這樣的寫法，等一下就會開一個去找GeneralApi::API這個class。GeneralApi這個module，裡面有一個class叫ApiV1。
這樣表示把`GeneralApi::ApiV1`掛在`/api/v1`下。雖然grape有支援版本號，可以在同一個class內打不同版本的api，但是如果所有的版本都打在同一個class裡，裡面的hepler一改動，會影響到其他版本，所以JC建議把不同的版本寫成不同的class。像這樣：
```ruby
mount GeneralApi::ApiV1 => '/api/v1'
mount GeneralApi::ApiV2 => '/api/v2'
...
```
這樣寫在routes.rb裡，如果版本很多，就會打很長一票，等一下會有比較常見的做法，先打一個最簡單的吧~

再來是重頭戲，`app/api/general_api.rb`的內容，就循序漸進來打吧，由簡至繁。
```ruby
module GeneralApi
  class ApiV1 < Grape::API
  	format :json

    desc "我的第一支grape api"

    params do
    end

    get '/sayhi' do
      {greeting: "Hi hi!"}
    end
  end
end
```
好了，這樣基本上可以快樂的跑`rails server`了，而且也完成了一個簡單的api。
在網址列輸入`localhost:3000/api/v1/sayhi`，console下會看到
`Started GET "/api/v1" for 127.0.0.1 at 2016-04-30 14:01:37 +0800`
瀏覽器會得到一個json，就是上面打的{greeting: "Hi hi!"}，在get下的最後一行可以直接帶hash就好，grape會自動轉成你要求的格式（此例是寫在最上面的format :json）。

**重點是，desc, params, get/post等三個為一組，不可少。desc是對此api的描述，params是參數，get是http method。**

再來說明params
```ruby
module GeneralApi
  class ApiV1 < Grape::API
    format :json

    desc "我的第一支grape api"

    params do
      requires :name, type: String, desc: "名字為必需"
      optional :gender, type: String, desc: "性別可有可無"
    end

    get '/sayhi' do
      title = ""
      if params[:gender] == "female"
        title = "小姐"
      elsif params[:gender] == "male"
        title = "先生"
      end
      {greeting: "Hi hi! #{params[:name]} #{title}"}

    end
  end
end
```
打這個`http://localhost:3000/api/v1/sayhi?name=sharefun`
```
{"greeting": "Hi hi! sharefun "}
```
打這個`http://localhost:3000/api/v1/sayhi?name=sharefun&gender=male`會得到
```
{"greeting": "Hi hi! sharefun 先生"}
```
而如果沒有給requires的東西，就會自動回錯誤，相當方便！
`http://localhost:3000/api/v1/sayhi?gender=male`
```
{"error": "name is missing"}
```
但是有打name但是給空值也是可以...也就是還要另外再過濾
`http://localhost:3000/api/v1/sayhi?name=&gender=male`
只要加上validator就行了！名字那行改成
```
requires :name, type: String, allow_blank: false, desc: "名字為必需"
```
可以參照文檔，還有許多的validator可用~


再來加一個helper玩玩~
```
module GeneralApi
  class ApiV1 < Grape::API
    format :json
    prefix ''

    helpers do
      def find_title(title, gender)
        if gender == "female"
          title = "小姐"
        elsif gender == "male"
          title = "先生"
        else
          title = ""
        end
      end
    end

    desc "我的第一支grape api"

    params do
      requires :name, type: String, allow_blank: false, desc: "名字為必需"
      optional :gender, type: String, desc: "性別可有可無"
    end

    get '/sayhi' do
      title = find_title(title, params[:gender])
      {greeting: "Hi hi! #{params[:name]} #{title}"}
    end
  end
end
```
基本上功能沒變，只是熟悉一下helper寫法。而prefix會掛在/api/v1下，如果prefix '123'，這個sayhi就會變成在/api/v1/123/sayhi。


再來我們分層mount。也就是routes.rb只mount一個class，該class再mount其他class。修改下列兩個檔案
routes.rb
```ruby
# mount GeneralApi::ApiV1 => '/api'
mount GeneralApi::API => '/api'
```

app/api/general_api.rb整個改成
```ruby
module GeneralApi
  class ApiV1 < Grape::API
    format :json
    prefix ''

    helpers do
      def find_title(title, gender)
        if gender == "female"
          title = "小姐"
        elsif gender == "male"
          title = "先生"
        else
          title = ""
        end
      end
    end

    desc "我的第一支grape api"

    params do
      requires :name, type: String, allow_blank: false, desc: "名字為必需"
      optional :gender, type: String, desc: "性別可有可無"
    end

    get '/sayhi' do
      title = find_title(title, params[:gender])
      {greeting: "Hi hi! #{params[:name]} #{title}"}
    end
  end

  class ApiV2 < Grape::API
    format :json

    desc "我的第二支grape api"

    params do
    end

    get '/sayhi' do
      {greeting: "大家好！"}
    end
  end

  class API < Grape::API
    mount GeneralApi::ApiV1 => '/v1'
    mount GeneralApi::ApiV2 => '/v2'
  end
end
```
原本的api的url基本保持不變，但我新增另一個第二版的sayhi，因為屬不同的class，改原本的helper不會影響第二版，但此分法不是絕對，可依個人喜好去調整。惟須記得class API要放在下面，不然會因為讀不到ApiV1而出錯。


補充：

因為跑rake，這些routes在routes.rb是找不到的。我找到一個別人寫好的lib可以讀grape api。放在`lib/task/routes.rake`下
```
namespace :grape do
  desc 'Print compiled grape routes of Airpopo'
  task :routes => :environment do
    GeneralApi::API.routes.each do |route|
      puts route
    end
  end
end
```
打rake grape:routes就可以看到目前的api routes。要注意，因為這邊只進去module裡看，所以寫在routes.rb裡的prefix不會被讀到，所以...自己心裡有底就好，我打的api是在`/api/v1/`而不是在`/`。

最後，因為打API有很多時候是要給別的網站串的，所以我們須要開放跨來源資源共享（CORS），詳見高手文章：
http://blog.toright.com/posts/3205/%E5%AF%A6%E4%BD%9C-cross-origin-resource-sharing-cros-%E8%A7%A3%E6%B1%BA-ajax-%E7%99%BC%E9%80%81%E8%B7%A8%E7%B6%B2%E5%9F%9F%E5%AD%98%E5%8F%96-request.html
https://developer.mozilla.org/zh-TW/docs/HTTP/Access_control_CORS

Rack based的app server可以用這邊提供的方法解掉。
https://github.com/cyu/rack-cors