參考ihower的Ruby on Rails實戰聖經的[ActionMailer - E-mail 發送](https://ihower.tw/rails4/actionmailer.html)章節。

在development.rb裡加入寄信的設定。
```
	...
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.default_url_options = { host:"http://localhost:3000" }
  config.action_mailer.smtp_settings = {
  	:address => "smtp.gmail.com",
		:port => "587",
		:domain => "gmail.com",
		:authentication => "plain",
		:user_name => "我的帳號@gmail.com",
		:password => "我的密碼",
		:enable_starttls_auto => true
  }
  ...
```

建立Mailer寄信程式，並嘗試寄給自己
```
> rails generate mailer UserMailer notify_comment

# In user_maiiler.rb
class UserMailer < ActionMailer::Base
	default :from => "我的帳號@gmail.com"

	def notify_comment
		mail(:to => "我的帳號@gmail.com", :subject => "New Comment")
	end
end

# 寄信
> rails c
> UserMailer.notify_comment.deliver_now!
```
但是出現錯誤`Net::SMTPAuthenticationError: 534-5.7.9 Application-specific password required.`

這是因為我使用了二階段驗證，如果要用gmail寄信，必須先從google設定App Password。

以下是解法網址[Sign in using App Passwords](https://support.google.com/accounts/answer/185833?hl=en)和[設定App Password的網址](https://security.google.com/settings/security/apppasswords)。

得到密碼後，直接取代掉development.rb中的`:password => "我的密碼"`即可。