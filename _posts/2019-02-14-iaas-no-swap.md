---
layout: post
title: "IaaS下的虚拟机通常没有swap"
permalink: /iaas-no-swap/
categories: iaas,swap,java
date: "2019-02-14 12:07:21 +0800"
---

* TOC
{:toc}

## 问题

我们的一个Java应用程序跑在docker中, 在自己的电脑上跑没什么问题，但是一运行在IaaS环境的VM中就会:

* 启动很慢(已经优化过springboot启动,加载太多依赖, 并且已经正确的在docker里配置了Xmx, 参见[javaopts-in-docker](https://github.com/westwin/javaopts-in-docker))
* 启动时一直报137 error (137 = 128 +9 ,即被docker系统强行杀掉), 从log里发现OOM Killed
  > Terminated at Jan 13, 2017 10:27:49 AM with exit code 137 (OOMKilled)
  > exit code 137 代表 128 + n, 即标准的shell的exit code,  Fatal error signal "n", 这里即代表发送了一个kill -9 <pid>. 
  > 参见Exit Codes With Special Meanings. OOMKilled代表OutOfMemory Killed. 如果给容器分配的内存太小的话，悲哀的java程序经常碰到这个

## 运行环境

* 某云的IaaS环境下运行我们的PaaS(k8s)
* PaaS里运行了一个上述我们自己的Java应用
* 该应用在容器里的memory limit 为设为512Mi
* 物理内存和虚内存的配比确定是1:1

## RCA

* IaaS环境下没有任何swap分区.
* 当手工把IaaS下的swap分区给加上时,[如何添加swap](https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-centos-7), 问题就解决了,该程序在memory limit 为512Mi的时候启动没有问题，并且启动速度在37s左右(37s还是很慢...程序的启动速度一定会限制在PaaS的弹性扩容)

## 公有云环境下的VM都没有swap分区

和IaaS的同事沟通了一下，他们说原因包括:

* 共有云环境下的虚拟机一律没有swap分区，阿里云和亚马逊云的虚拟机都没有swap
* 建议客户尽量使用物理内存，以获取更高的性能
* 客户使用物理内存，能够更方便的计费

## 经验和教训

* Java的应用程序一般都会消耗稍多的内存，启动脚本务必设置正确的-Xmx来限制memory的大小
* 今后开发/测试环境尽可能的保持和IaaS团队的环境保持一致，即和prod环境一致