---
layout: post
title: "Nginx file permission denied"
permalink: /nginx-permission-denied-selinux/
categories: linux selinux nginx httpd
date: "2018-05-07 09:48:21 +0800"
---

* TOC
{:toc}
selinux: Nginx file permission denied

## 问题

CentOS下，装了一个全新的nginx, 把一些文件放到缺省的Document_Root目录下(/usr/share/nginx/html), 从浏览器端无法访问，nginx日志
总是报 **file permission denied**.

一开始以为是nginx进程的运行用户nginx没有权限访问那些文件，尝试chmod仍然无效. 
后来突然想起可能和 **selinux** 相关

## selinux

1. 尝试运行命令```chcon -Rt httpd_sys_content_t /usr/share/nginx/html```, 问题得以解决
2. man chcon: change file SELINUX security context
    * ```-R```代表recursive
    * ```-t```代表selinux context type
3. ls查看一个文件的selinux context, ```ls -Z```

## 参考

请参考[Managing confined services the apache http server](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/managing_confined_services/sect-managing_confined_services-the_apache_http_server-types)

> **httpd_sys_content_t**
>
> Use this type for static web content, such as .html files used by a static website. Files labeled with this type are accessible (read only) to httpd and scripts executed by httpd. By default, files and directories labeled with this type cannot be written to or modified by httpd or other processes. Note that by default, files created in or copied into /var/www/html/ are labeled with the httpd_sys_content_t type.
