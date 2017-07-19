---
layout: post
title:  "ruby 获取远程图片jpg/png 的url,转换为base64"
date:   2016-12-07
categories: rails
comments: true
---



```ruby
  # ruby 获取远程图片jpg/png 的url,转换为base64
  def base64
    require 'open-uri'
    tempfile = open('http://ofkzuey9y.bkt.clouddn.com/bala-logo.png')
    image_base64 = Base64.encode64(File.read(tempfile))
  end

```