---
layout: post
title: "Gradle GFW"
permalink: /gradle-gfw/
categories: gfw gradle
date: "2019-02-14 12:00:21 +0800"
---

* TOC
{:toc}

gradle由于GFW的原因经常无法下载依赖，可以尝试在gradle中配置翻墙代理服务器

在$HOME/.gradle/gradle.properties中添加如下配置(注意这是全局配置，对所有的gradle工程生效)

```bash
systemProp.http.proxyHost=<proxy_ip>
systemProp.http.proxyPort=<proxy_port>
systemProp.http.nonProxyHosts=localhost|127.0.0.1|10.10.1.*
systemProp.https.proxyHost=<proxy_ip>
systemProp.https.proxyPort=<proxy_port>
systemProp.https.nonProxyHosts=localhost|127.0.0.1|10.10.1.*
```

需要用实际的代理服务器ip和端口号替代上面的配置

nonProxyHosts代表不想通过proxy访问的网络ip地址段