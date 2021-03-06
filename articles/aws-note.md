# AWS 新手指南

免費方案注意事項：
[Amazon EC2免费VPS防止超额被扣钱三大方法:流量 硬盘读写 运行时长](http://www.freehao123.com/amazon-ec2/)

[Amazon AWS —— 免费的午餐不好吃](http://bropaul.com/post/amazon-aws-in-practice)

總結：
Amazon [EBS](https://aws.amazon.com/tw/ebs/)（提供EC2儲存空間的服務）會charge硬碟的I\/O，如果EC2 instance大量讀寫硬碟，就會收高額費用
解法有三

1. 使用cache機制（使用他們的memcached or redis）
2. db使用他們的RDS，不要直接掛在EC2上  
3. Assets放在S3或其他CDN

簡單介紹
[AWS 各項服務](http://blog.jex.tw/blog/2014/10/29/aws/)

[AWS相關專用名詞](http://lp43-aws.blogspot.tw/2012/05/aws.html)

[AWS 常用功能简介及 EBS 性价比分析](https://blog.laisky.com/p/aws-ec2-ebs/)


[有關AWS EC2 \(EBS 收費\)的問題](http://blog.wabow.com/archives/2634)

[Amazon AWS 實戰筆記與心得](https://blog.hinablue.me/entry/aws-working-with-amazon-aws/)

[AWS 使用小记－ Beanstalk + Rails + Passenger + PostgreSQL](http://cocacolacat.github.io/2016/03/04/setup-aws-beanstalk-rails-pg.html)

[適用於 Ruby 的 AWS 開發套件](https://aws.amazon.com/tw/sdk-for-ruby/)

Network setting
[AWS VPC 設定教學](http://blog.yslin.tw/2014/02/aws-vpc.html)

系列文
[一年免費玩盡 Amazon 雲端服務](http://www.hkitblog.com/?p=24354)


SSL

[AWS ELB SSL 憑證設定](https://tzangms.com/aws-elb-ssl-certificate-setup/)

[AWS 筆記 - 使用 Amazon Elastic Load Balancing 與 SSL\/HTTPS \(GoDaddy\) 設定](http://blog.changyy.org/2014/04/aws-amazon-elastic-load-balancing.html)

[AWS Load Balancers SSL\/HTTPS 設定](http://jade.logdown.com/posts/233257-aws-load-balancers-set-ssl-https)

[AWS EC2 Load Balancer上設定SSL憑證](http://www.petekcchen.com/2014/06/configure-ssl-on-aws-ec2-load-balancer.html)



S3設定
[Rails 上傳 Upload](http://blog.jex.tw/blog/2015/07/13/rails-upload/)
[Paperclip with Amazon S3](https://github.com/thoughtbot/paperclip/wiki/Paperclip-with-Amazon-S3)

傳資料夾要加--recursive