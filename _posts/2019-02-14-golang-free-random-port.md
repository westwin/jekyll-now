---
layout: post
title: "Golang Free Port"
permalink: /golang-free-port/
categories: golang network
date: "2019-02-14 15:28:21 +0800"
---

* TOC
{:toc}
Golang Free Port

## Overview

随机选择一个本地没有被占用的端口号, 代码如下,

```go
func freePort() int {
    addr, err := net.ResolveTCPAddr("tcp", "localhost:0")
    if err != nil {
        panic(err)
    }

    l, err := net.ListenTCP("tcp", addr)
    if err != nil {
        panic(err)
    }
    defer l.Close()
 
    return l.Addr().(*net.TCPAddr).Port
}
```

## 原理

The port 0 trick