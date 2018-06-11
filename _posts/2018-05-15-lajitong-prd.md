---
layout: post
title: "lajitong PRD"
permalink: /lajitong-prd/
categories: lajitong 
date: "2018-05-15 10:14:21 +0800"
---

* TOC
{:toc}
[lajitong]: Product requirements document

# Background

pastebin.com作为一个分享源代码的网站，在国内无法访问. 所以萌发出做一个国内的pastebin的想法.

## Basic Features

1. post相关功能
    * send post
      * anonymous post
    * view post
      * syntax highlighting
      * raw post
      * share post
    * post persistence
      * forever
      * ttl
    * post visibility
      * public
      * unlisted
2. 搜索
    * 根据关键字全文搜索
    * 根据tag进行分组
3. delete post

## Advanced Features

1. 安全相关
    * post内容审计
      * 关键字过滤
      * anti spam
      * flooding post

## No Features

1. 用户
    * 全部匿名用户操作
2. 热点
    * hit counter
    * popular post
3. URL shortening

[lajitong]: https://github.com/westwin/lajitong