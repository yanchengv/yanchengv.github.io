---
layout: post
title:  "开发gem"
date:   2016-11-29 14:44
categories: jekyll update
comments: true
---


### 1.创建gem
```ruby
 bundle gem mygem
```

### 2.生成gem

``` ruby
$ rake build

mygem 0.0.1 built to pkg/mygem-0.0.1.gem.
```

### 3.第一个gem诞生了。它就在当前目录的pkg下：mygem-0.0.1.gem。如何使用呢？不考虑bundler的情况下，如果你开起了一个irb或者pry的session时，一般都会这样写：require "mygem"，如果你现在这样做，那肯定不行，因为它还没有被安装到ruby的load path中，那就把它安装上。

``` ruby
$ rake install

mygem 0.0.1 built to pkg/mygem-0.0.1.gem.
mygem (0.0.1) installed.

```
### 4.安装好了，那就来使用一下，打开irb：

```ruby
require "mygem"
=> true
Mygem
=> Mygem
```

### 5.项目中测试本地gem:

```ruby
gem 'mygem',:path => '/home/yan/RubymineProjects/mygem'

bundle install
```




### 6.已经可以使用这个module了，不过这个gem啥也干不了，那么我们就给它添加一个方法吧，打开lib/mygem.rb，添加一个方法：

```ruby
require "mygem/version"

module Mygem
  def self.hello
    p "hello from my gem"
  end
end
```
### 7.保存，然后执行rake install，这个命令会先build然后install，再重新打开irb：

```ruby
require "mygem"
=> true
Mygem.hello
=> "hello from my gem"

```