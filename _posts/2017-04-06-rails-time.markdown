---
layout: post
title:  "rails时间处理"
date:   2017-03-30 17:24
categories: rails
comments: true
---

```ruby
    #rails时间处理

    Time.now + 5.days

    Time.now + 1.day

    Time.now - 1.year

    # 时间的定位方法

    Time.now.beginning_of_week

    Time.now.end_of_month

    time_start = Time.now.beginning_of_month  #当前日期所在月的开始时间

    time_end = Time.now.end_of_month   #当前日期所在月的终止时间

    Date.today.end_of_week(:saturday) #定位到本周六


```