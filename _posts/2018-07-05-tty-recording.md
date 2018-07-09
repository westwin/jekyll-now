---
layout: post
title: "tty录制屏幕"
permalink: /ttyrec-ttygif/
categories: linux cli
date: "2018-07-05 14:51:21 +0800"
---

* TOC
{:toc}
tty录制屏幕

# tty录屏/生成gif

1. 开始录屏: ```ttyrec myrecording```
2. 任意操作
3. 结束录屏:```exit```
4. 转成gif:```ttygif myrecording```
5. 参考:
    * [ttygif](https://github.com/icholy/ttygif)
    * [ttyrec](http://0xcc.net/ttyrec/)
    * [ttyrec fix on centos](https://github.com/mjording/ttyrec/issues/3)
6. ![Demo](/images/tty.gif)
7. 更高级的工具见[asciinema](https://asciinema.org/)
