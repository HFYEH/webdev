```
gem install guard-livereload

 # in Gemfile
group :development do
  gem 'guard'
  gem 'guard-livereload'
end

bundle

guard init livereload

guard

rails s
```