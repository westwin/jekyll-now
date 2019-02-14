---
layout: post
title: "在Mongo查询中使用正则表达式"
permalink: /mongodb-regex/
categories: mongo
date: "2019-02-14 15:31:21 +0800"
---

* TOC
{:toc}
在Mongo查询中使用正则表达式

## 语法

MongoDB 使用 $regex 操作符来设置匹配字符串的正则表达式。

1. 基本用法

    如：查询名字中包含字符串“abc”的文档记录：

    ```go
    db.connectors.find({"name": {"$regex":"abc"}})
    ```

2. 前匹配

    ```go
    db.connectors.find({"name": {"$regex":"^abc"}})
    ```

3. 后匹配

    ```go
    db.connectors.find({"name": {"$regex":"abc$"}})
    ```

4. 忽略大小写

    ```go
    db.connectors.find({"name": {"$regex":"^abc", "$options": "i"}})
    ```

## 注意事项

### 性能

在设置索引的字段上进行正则匹配可以提高查询速度，而且当正则表达式使用的是前缀表达式时，查询速度会进一步提高。

### 防止正则注入

如，一个正常的URL调用：http://localhost:8080/api/v1/connectors?q=abc&page=1&size=10

注入调用：http://localhost:8080/api/v1/connectors?q=^&page=1&size=10

导致的结果是查询的结果与预期不符。

措施：对传入的查询条件做转义。

go语言的示例：使用[regexp.QuoteMeta函数](https://godoc.org/regexp#QuoteMeta)