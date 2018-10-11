---
title: 启动/安装shadowsocks命令(客户端、服务器端)
layout: post
date: 2017-06-05 17:24
categories: shadowsocks
comments: true
---

## 	命令行安装客户端 :
#### 	 Mac OSX:
	
> * 	`brew install shadowsocks-libev`
> *   `pip install shadowsocks`
> *  ` cpan Net::Shadowsocks`

#### 	 Linux:
	
> * 	`pip install shadowsocks`
> *   `apt-get install shadowsocks-libev`
> *  `cpan Net::Shadowsocks`

## 	启动命令 :

> * 服务器端启动命令：``` ssserver -c /etc/shadowsocks.json ```

> * 客户端启动命令：``` ss-local -c /etc/shadowsocks_client.json ```
  

参考：
* [shadowsocks.org](https://shadowsocks.org/en/download/clients.html)
* [github-shadowsocks](https://github.com/shadowsocks/shadowsocks/tree/master)