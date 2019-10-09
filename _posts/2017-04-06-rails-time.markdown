---
layout: post
title:  "rails时间处理"
date:   2017-03-30 17:24
categories: rails
comments: true
---

* 时间的定位方法

    ```ruby

    Time.now + 5.days
    
    Time.now + 1.day
    
    Time.now - 1.year

    Time.now.beginning_of_week #当前日期所在的周的开始时间

    Time.now.end_of_week #当前日期所在的周的的结束时间

    time_start = Time.now.beginning_of_month  #当前日期所在月的开始时间

    time_end = Time.now.end_of_month   #当前日期所在月的终止时间

    Date.today.end_of_week(:saturday) #定位到本周六
  
    "2019-10-09 12:00:00 -0800".to_time.localtime #西八区的时间转换成本地时间

    ```


* 计算两个时间相差的天数
    
    ```ruby
    DateTime.parse("2007-12-28") - DateTime.parse("2007-12-26")=>2
    ```

* 计算两个时间相差的秒数

    ```ruby
    (DateTime.parse("Dec  2 03:12:19 2005") - DateTime.parse("Dec 2 14:44:47 2005")) * 24 * 60 * 60 =>-41548
    ```
* 计算两个时间相差的分钟数

    ```ruby
    (DateTime.parse("DateTime.parse(Time.now.strftime("%Y-%m-%d %H:%M:%S")") - DateTime.parse("2016-5-12 15:20:20")) * 24 * 60  => (1321/20)
    ```
