---
layout: post
title: "cent7 下用firewall-cmd命令来管理防火墙"
permalink: /centos-firewall-cmd/
categories: centos
date: "2019-03-28 15:22:21 +0800"
---

* TOC
{:toc}
cent7 下用firewall-cmd命令来管理防火墙

## 背景

Cent 6.x中用来管理防火墙的iptables在cent7中已经被firewalld来代替，取而代之的命令行管理工具为firewall-cmd

通常，我们的需求比较简单， 也就是说启用或禁用某些著名端口(比如80,443,8080), 当然项目中也引入了其他的端口，比如my-micro-service的8085端口

## 可行的方法

那么，我们的需求就转化为 如何用firewall-cmd来启用这些端口.

1. 方法1: 直接启用端口

    ```bash
    # enable my-micro-service tcp port 8085
    sudo firewall-cmd --zone=public --permanent --add-port=8085/tcp
    ```

    注：上述命令行中的--zone=public代表的是cent7中缺省打开的zone是public。 zone的概念也是在cent7中新引入的，详细参考最下面的链接。
2. 方法2: 添加自定义的firewalld的service, 在service里映射这些端口号，然后启用service

    ```bash
    # define a firewalld service for my-micro-service
    sudo vi /etc/firewalld/services/my-micro-service.xml
    
    <?xml version="1.0" encoding="utf-8"?>
    <service>
    <short>my-micro-service</short>
    <description>My Micro Service.</description>
    <port protocol="tcp" port="8085"/>
    </service>
    
    # reload this new service
    sudo firewall-cmd --reload
    
    # enable this service
    sudo firewall-cmd --zone=public --add-service=my-micro-service --permanent
    ```
3. 方法3: 自定义一个zone。 比较麻烦，不做介绍了

方法2比较优雅，又相对简单

修改想要立即生效，可以运行 ```sudo firewall-cmd --reload```

## 参考

* [Set Up a Firewall using Firewalld on Centos 7](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7)