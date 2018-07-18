---
layout: post
title: "True SSO - PAM & LDAP"
permalink: /true-sso-pam-ldap/
categories: sso pam ldap
date: "2018-07-18 10:34:21 +0800"
---

* TOC
{:toc}
True SSO - PAM & LDAP

## 背景

SSO最常用的用户场景是各种应用之间的SSO, 尤其是基于浏览器的各种Web应用之间的SSO.

而广义上的SSO, 即所谓的True SSO还可以用来管理:

* Linux虚拟机各种需要用户名/密码的命令行应用，比如ssh, sudo, login, passwd, su等
* 接入WiFi时的身份认证
* 打印机接入
* DNS解析
* 等等，其他基础设施(软件，硬件，网络设备, IOT等)

本文着重提一下，如何在linux上通过PAM, 用在LDAP里存储的用户名和密码，通过SSH来登录Linux机器

## 方法和工具

1. ldap server
   * 要求用户具有objectclass: posixAccount, shadowAccount
   * 要求用户组具有objectclass: posixGroup
   * 示例: ![posix-shadow-account](/images/posix-shadow-account.png)

2. /etc/pam.d/
   * pam_ldap.so
   * pam_mkhomedir.so(用户首次登录，自动创建homedir)

3. nss-pam-ldapd
   * nslcd

具体步骤不再赘述，参考man nslcd.conf进行配置.

配置成功后，可以做如下测试:

* getent passwd <your_username_in_ldap>: 查看该用户的信息，从ldap服务器获取
* ssh <your_username_in_ldap>@<your_server>: 用ldap服务器上的用户名和密码进行ssh登录

## 关键字

google关键字 PAM LDAP

PAM编程参考: [The Linux-PAM System Administrators' Guide](http://www.linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html)

## 坑

* 貌似nslcd的配置文件ldap的uri不支持自定义端口号(比如uri: ldap://10.31.196.200:1389/， 总是报Can't contact LDAP server: Transport endpoint is not connected)

## 思考

基于PAM编程接口，理论上我们可以扩展出基于OAuth2/OIDC协议的支持, TBD
