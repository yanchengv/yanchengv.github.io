---
title: ruby on rails 导出csv中文乱码
layout: post
date: '2017-12-15 21:38:37'
categories: rails
---

最近rails  做csv导出，碰到了中文乱码问题，解决方法如下
```
header = ‘EF BB BF’.split(' ').map{|a|a.hex.chr}.join()  
CSV.generate do |csv|
	 csv << header
	 csv << ['姓名','手机']
	 csv << ['内容1','内容2']
end
```

但是以上解决方法会在文件上方多出一行。其实上述方法可以写为：

```
CSV.generate do |csv|
	 csv <<  ["\xEF\xBB\xBF姓名","手机"]
	 csv << ['内容1','内容2']
end
```

这里要注意的是ruby字符串可以用单引号，和双引号。区别就不多说了，这里引用16进制串的就必须用双引号（否则依旧乱码），其他的没事，你懂的。
 
至此搞定！其他什么都不要弄，就在开始加个\xEF\xBB\xBF，这个需要放到双引号中.