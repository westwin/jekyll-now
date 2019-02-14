---
layout: post
title: "Telnet Alternative to Ping a TCP/UDP Port"
permalink: /ping-port/
categories: linux
date: "2019-02-14 18:25:21 +0800"
---

* TOC
{:toc}
Telnet Alternative to Ping a TCP/UDP Port

```bash
#!/bin/bash
# telnet alternative to ping a tcp port
# to ping udp port, use /dev/udp/$host/$port
## NOTE: this is a bash hack, not work in zsh.

host=$1
port=$2
echo >/dev/tcp/$host/$port
```
