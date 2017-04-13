### Semantic Versioning

x.y.z

x變動代表可能向後不相容
y變動代表有功能新增
z變動代表bugs修正

### Gemfile

第一次bundle install或存在Gemfile.lock情情下bundle update的gem更新情況

```
gem 'rails', '4.2.1'           # 指定安裝此版本
gem 'rails'                    # 會裝最新版
gem 'rails', '~>4.2.1'         # 會安裝4.2.x中的最新版本
gem 'authlogic', :git => 'git://github.com/odorcicd/authlogic.git', :branch => 'rails3'  # 指定github repo

[ref 1](https://ihower.tw/rails/environments-and-bundler.html) and [ref 2](https://ruby-china.org/topics/26655)
```

### Package.json

```
{ "dependencies" :
  {
    "xxx" : "2.1.1",
    "foo" : "~1.0.0",     # 1.0.x
    "boo" : "^2.0.1",     # 2.x.x
    "yyy" : "git://github.com/yutin1987/react-native-mapbox-gl.git#mapbox2"
  }
}
```