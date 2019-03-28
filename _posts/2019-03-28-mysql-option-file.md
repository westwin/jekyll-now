---
layout: post
title: "MySQL命令行下用密码连接数据库的常用做法"
permalink: /mysql-option-file/
categories: mysql 
date: "2019-03-28 13:53:21 +0800"
---

* TOC
{:toc}
MySQL命令行下用密码连接数据库的常用做法

## 背景

在日常的开发，测试，部署过程中，我们经常需要通过命令行连接MySQL数据库，而连接往往需要密码.

那么存在一个问题，如何更安全的保护这个密码?

比如在实际一个项目中用到了一个叫erp的数据库, 连接连接密码是mypassword

## 常用做法

* 把密码作为参数传入命令行

    ```mysql -udbuser -pmypassword erp```

    直接把密码作为命令行的一个参数来使用，这样通过history命令查看历史很容易就找到密码了，所以这种做法也是最不推荐的做法，

    运行完这条命令之后，MySQL自己也会友情提示 Warning: Using a password on the command line interface can be insecure.
* 用户交互方式，手动输入密码

    通过用户交互的方式，让用户输入密码, mysql -udbuser -p  , 但是这么做对一些自动化脚本不太友好.

## 推荐做法

MySQL提供了一个所谓的option file，在这个option-file按照固定的格式把连接MySQL用到的密码等参数放进去，比如:

```conf
[mysql]
password=mypassword
```

可以对这个文件做一些访问权限等的限制,比如chmod 600. 用时生成这个文件，用完删掉...

然后连接mysql时，用 ```mysql --defaults-file=<option-file-dir>```, 具体可以参考 [Using Option Files](http://dev.mysql.com/doc/refman/5.7/en/option-files.html)