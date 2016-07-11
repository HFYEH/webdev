---
layout: post
title: 'sublime text 3'
date: 2015-01-26 12:07
comments: true
categories: 
---
##Settings-User
```

{
	"color_scheme": "Packages/Solarized Color Scheme/Solarized (dark).tmTheme",
	"font_size": 10,
	"ignored_packages":
	[
		"Vintage"
	],
	"tab_size": 2,
	"translate_tabs_to_spaces": true
}
```

##Install Package Control
ctrl+`
```
import urllib.request,os,hashlib; h = '2deb499853c4371624f5a07e27c334aa' + 'bf8c4e67d14fb0525ba4f89698a6d7e1'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
之後可以透過 `ctrl` + `Shift` + `p` 叫出來選單。

`install`安裝
`list`查看已安裝的外掛

##Submlime Text 套件

###BeautifyRuby
自動對 Ruby 縮排
`Ctrl` + `alt` + `k` 自動對 Ruby 縮排

###Color Solarize
選擇 Solarized Theme 佈景安裝。再透過 Preferences -> Color Scheme，換成 Solarize 布景

###ConvertToUTF8
解決 Sublime Text 3 預設不支援 Big5 與其他 CJK 編碼的問題

###BracketHighlighter
讓你在撰寫如HTML程式碼時，能有高亮度提醒功能。

###Emmet
Zen-coding

###DocBlockr 
自動產生區塊是註解的工具，可用在 JS, CSS, PHP, CoffeeScript, Actionscript, C & C++ 等等

###SublimeTmpl
範本產生器

###SideBarEnhancements
提供許多側邊攔 (SideBar) 的右鍵選單功能，非常實用！ ( 按下 `Ctrl`+`K`+`B` 可顯示/隱藏側邊攔 )

###Git
Git控制台，可直接在sublime中執行git的指令

###GitGutter
顯示新增/刪除/修改的程式碼區塊

###JsFormat
JS程式碼格式工具

###jquery

###ColorPicker
呼叫調色盤，讓你直接用選的來選顏色。當然，用在CSS居多。
`Ctrl`+`Shift`+`c`

###SCSS
延伸Sublime Text撰寫SCSS的能力。
