---
layout: post
title: "CentOS7编译最新的curl以支持https proxy"
permalink: /build-curl-on-centos7/
categories: linux proxy https
date: "2018-04-20 14:10:21 +0800"
---

* TOC
{:toc}
CentOS7下编译最新版本的curl, 以支持https proxy

## HTTPS proxy with curl

CentOS7下面，根据[HTTPS PROXY WITH CURL](https://daniel.haxx.se/blog/2016/11/26/https-proxy-with-curl/),老版本的curl是不支持**https proxy**的， 只支持http/socks的proxy  (苹果电脑的最新的curl支持https proxy)

如下尝试在CentOS7.4.1708的docker容器里编译最新版本的curl, 步骤如下（**注意**：切勿在自己的机器上升级系统原生的curl, 会出各种问题。所以我们在docker 容器里折腾):

1. yum groupinstall "Development Tools"
2. yum -y install groff krb5-devel libidn-devel libssh2-devel nss-devel openldap-devel openssh-clients openssh-server pkgconfig stunnel zlib-devel rpm-build ntpdate perl-Time-HiRes perl-Digest-MD5
3. git clone https://github.com/curl/curl.git
4. ./buildconf
5. 缺省编译的prefix是/usr/local，以免和系统自带的/usr/bin/curl冲突: ./configure
6. make
7. make install (安装后的在/usr/local/bin/curl)
8. test: /usr/local/bin/curl --version
9. send_proxy_req: /usr/local/bin/curl -k --proxy-insecure -x 'https://proxy_ip:proxy_port' --proxy-header "Proxy-Authorization: blabla" https://target_server_ip -v