---
title: 启动/安装shadowsocks命令(客户端、服务器端)
layout: post
date: 2017-06-05 17:24
categories: shadowsocks
comments: true
---

## 	一、服务器端安装(ubuntu) :

###  1.shadowsock搭建

```
1.apt-get install python-pip python-setuptools

2.pip install wheel

3.pip install git+https://github.com/shadowsocks/shadowsocks.git@master

```

### 2.shadowsock配置

创建 `/etc/shadowsocks.json`文件：
```
{
    "server":"45.76.15.126",
    "local_address": "127.0.0.1",
    "local_port":1080,
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false,
    "port_password": {
        "8382":" yan2019",
        "8383": "yan2019#"
    }
}
```

### 3.启动命令 :
> * 服务器端启动命令：``` ssserver -c /etc/shadowsocks.json ```

 > * 服务器端启动后台运行命令：``` ssserver -c /etc/shadowsocks.json -d start ```
 
 > * 服务器端关闭后台运行命令：``` ssserver -c /etc/shadowsocks.json -d stop ```


## 	二、命令行安装客户端 :
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