---
layout: post
title: "Password Storage Scheme"
permalink: /password-storage-scheme/
categories: token,sso 
date: "2019-02-14 14:59:21 +0800"
---

* TOC
{:toc}
Password Storage Scheme

##问题

当"主用户中心"同步"第三方用户中心"里的用户数据时，可能会需要把"第三方用户中心"中的"用户密码"也同步过来. 通常情况下，"用户密码"不会以明文的形式存储在"第三方用户中心"中，而是以"某种密文"的方式加以存储，常见的"某种密文"包括"对称加密"以及"单向Hash"的方式.

那么问题就来了，同步"用户密码"的时候，仅仅同步加密后的密文是不够的，同时还需要同步"对称加密"的密钥(以及对应的加密算法), 以及"单向Hash"的算法(以及可能的salt等).

当同步完成时，对于"主用户中心"来说, 用户来做AuthN时，主用户中心需要用不同的加密算法来验证用户名和密码.

在OpenDJ里，对应的解决方案就是"Password Storage Scheme"的概念，根据需要，我们可以自定义一些"密码加解密"的Plugin, 集成到OpenDJ中.

比如在某"第三方用户中心"里，用户的密码加密是他们自定义的一种md5(fixed_prefix + plain_pwd + fixed_suffix), 那么当把它里的用户导入到主用户中心时,我们需要自定义酱紫的一个Plugin,并集成到OpenDJ中(杨望星干过类似的事情)

## OpenDJ中的userPassword怎么存

简单来说和密码相关的所有细节(包括密文以及加密可能用到的key/salt等)都是存在userPassword这么一个属性中去的，该userPassword需要遵循相应的scheme

比如{SSHA}encoded_pwd+fix_lengthed_salt

细节请参考https://blogs.oracle.com/DirectoryManager/entry/the_ssha_password_storage_scheme

## Password Storage Scheme的参考实现

[参见我的golang版本的实现](https://github.com/westwin/pss)