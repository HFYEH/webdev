gem "kaminari" 分頁套件

gem 'therubyracer' Javascript直譯器(也可以用Node.js)

gem 'bootstrap-sass'

gem 'devise' 使用者認證

如果安裝nokogiri失敗
gem install nokogiri -- --use-system-libraries


# search
ransack
sphinx - offline search
algolia -

# Authority:
rolify cancancan


# 排程
whenerve
delayed_job
sidekick

# pdf
wicked_pdf
# https://github.com/thoughtbot/paperclip/wiki/PDF-Email-Attachments-with-Paperclip-and-wicked_pdf



# Guard
```
group :development do
 gem 'guard'
 gem 'guard-livereload'
end
```

第一次使用時，在專案目錄下跑guard init livereload，再跑guard，就可以監聽資料夾更動自動發出更新訊息，browser只要裝livereload extenstion就可以了~



```
source 'https://rubygems.org'

gem 'rails',                   '4.2.0'
gem 'bcrypt',                  '3.1.7'
gem 'faker',                   '1.4.2'
gem 'carrierwave',             '0.10.0'
gem 'mini_magick',             '3.8.0'
gem 'fog',                     '1.23.0'
gem 'will_paginate',           '3.0.7'
gem 'bootstrap-will_paginate', '0.0.10'
gem 'bootstrap-sass',          '3.2.0.0'
gem 'sass-rails',              '5.0.1'
gem 'uglifier',                '2.5.3'
gem 'coffee-rails',            '4.1.0'
gem 'jquery-rails',            '4.0.3'
gem 'turbolinks',              '2.3.0'
gem 'jbuilder',                '2.2.3'
gem 'sdoc',                    '0.4.0', group: :doc

group :development, :test do
  gem 'sqlite3',     '1.3.9'
  gem 'byebug',      '3.4.0'
  gem 'web-console', '2.0.0.beta3'
  gem 'spring',      '1.1.3'
end

group :test do
  gem 'minitest-reporters', '1.0.5'
  gem 'mini_backtrace',     '0.1.3'
  gem 'guard-minitest',     '2.3.1'
end

group :production do
  gem 'pg',             '0.17.1'
  gem 'rails_12factor', '0.0.2'
  gem 'unicorn',        '4.8.3'
end
```