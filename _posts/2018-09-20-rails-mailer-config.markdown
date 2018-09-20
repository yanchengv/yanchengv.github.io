---
title: rails发送邮箱配置
layout: post
date: '2018-09-20 09:30:00'
categories: rails
---

rails正式环境配置邮箱。
企业邮箱有两个端口25（非ssl）和465（ssl加密）。配置错误，可能会导致本地发送成功，但是部署到正式环境会失败，所以确定服务器是否使用ssl。

`config/environments/production.rb`

```
  config.action_mailer.raise_delivery_errors = true
  config.action_mailer.perform_caching = false
  config.action_mailer.perform_deliveries = true
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = {
      address:              smtp.263.net,
      port:                 25, #如果服务器使用ssl加密(https)那么要注意，端口是465.
      domain:               263.com,
      user_name:            admin@263.com邮箱,
      password:             邮箱登陆密码,
      authentication:       :login,
      enable_starttls_auto: true,
      ssl:                   true #如果服务器使用ssl加密(https)那么要注意，:ssl一定要设置！:openssl_verify_mode 一定不能配置！
  }
```