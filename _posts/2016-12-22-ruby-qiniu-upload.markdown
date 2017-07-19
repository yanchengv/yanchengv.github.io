---
layout: post
title:  "七牛远程上传:远程获取图片的url 上传到七牛"
date:   2016-12-22 13:00:08
categories: rails 七牛
comments: true
---


```ruby

  def self.qiniu_fetch media_id
    # 注意！注意！慎重使用$weixin.is_valid?方法，它会重新获取access_token,access_token每天只有2000次
    from_url ="http://www.balawo.com/pic/1.jpg"
    key = "2.jpg" #上传后文件的名字
    encode_from_url = Qiniu::Utils.urlsafe_base64_encode(from_url)
    encode_to_uri = Qiniu::Utils.encode_entry_uri(Settings.qiniu.bucket, key)
    path = "/fetch/#{encode_from_url}/to/#{encode_to_uri}"
    host = "iovip.qbox.me"
    access_token = Qiniu::Auth.generate_acctoken(path)
    authorization = "QBox #{access_token}"
    result = HTTParty.post("http://#{host}/fetch/#{encode_from_url}/to/#{encode_to_uri}",
                           :headers => {'Content-Type' => 'application/json', 'Authorization' => authorization})
    url = Settings.qiniu.qiniu_base_url + result['key']
  end

```