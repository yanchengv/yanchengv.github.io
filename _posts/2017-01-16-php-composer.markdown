---
layout: post
title:  "安装php composer"
date:   2017-01-16 17:24
categories: Lorem
comments: true
---

```ruby
# 安装php composer

Step 1:

Run
$ sudo apt-get update
If this was not done before.

Step 2:

Install wget for file download in ubuntu
$ sudo apt-get install wget

Step 3:

Download the composer.phar from https://getcomposer.org/download via wget or built-in curl
$ wget https://getcomposer.org/composer.phar
or
$ curl -O https://getcomposer.org/composer.phar

** -O is capital O not Zero(0)

Step 3:

Rename composer.phar to composer
$ mv composer.phar composer

Step 4:

Make composer executable
$ chmod +x composer

Step 5:
Now composer can be run locally through
$ ./composer
But this is available where you are i.e. current directory where the composer file is.

Step 6:

Make composer available globally move it to /user/local/bin by
$ sudo mv composer /usr/local/bin
Now composer will be available anywhere by simply
$ composer

```