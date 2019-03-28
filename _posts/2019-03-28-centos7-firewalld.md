---
layout: post
title: "cent7下安装ss"
permalink: /centos-install-ss/
categories: centos
date: "2019-03-28 14:18:21 +0800"
---

* TOC
{:toc}
cent7 install ss

## python ss

cent7里有个python版本的ss, 所以

1. 需要先安装python相关的一些东西

    ```bash
    sudo yum install python-setuptools && sudo easy_install pip
    sudo pip install shadowsocks
    ```

2. 配置ss

    ```bash
    sudo mkdir -p /etc/shadowsocks
    sudo vi /etc/shadowsocks/config.json
    # 添加你的实际配置
    ```

3. 开机自启shadowsocks

    ```bash
    sudo vi /etc/systemd/system/shadowsocks-server.service
    
    #input below contents
    [Unit]
    Description=Shadowsocks Server
    After=network.target
    
    [Service]
    Type=forking
    PIDFile=/run/shadowsocks/server.pid
    PermissionsStartOnly=true
    ExecStartPre=/bin/mkdir -p /run/shadowsocks
    ExecStartPre=/bin/chown root:root /run/shadowsocks
    ExecStart=/usr/bin/sslocal --pid-file /var/run/shadowsocks/server.pid -c /etc/shadowsocks/config.json -d start
    Restart=on-abort
    User=root
    Group=root
    UMask=0027
    
    [Install]
    WantedBy=multi-user.target
    ```

    注意: 这里有个坑。网上的文章说用/usr/bin/ssserver来启动我们的这个代理，实际上根本起不来，ExecStart那用的是/usr/bin/sslocal就可以启动了。。

    ```bash
    sudo systemctl start shadowsocks-server.service
    sudo systemctl enable shadowsocks-server.service
    ```
4. 浏览器安装proxy插件，配置gfwlist

    ff下可以用插件foxyproxy, chrome下用SwitchyOmega , 这个就不说了，网上一大把教程.

    貌似linux下的shadowsocks不支持全局代理？所以需要在浏览器里配代理，默认1080端口socks5