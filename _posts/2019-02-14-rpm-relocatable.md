---
layout: post
title: "Relocatable RPM"
permalink: /relocatable-rpm/
categories: linux,rpm
date: "2019-02-14 14:40:21 +0800"
---

* TOC
{:toc}
Relocatable RPM

所谓relocatable的RPM是指该RPM可以被安装到指定的目录. not-relocatable反之.

当然，不是所有的RPM都是relocatable的

查看一个RPM是否是relocatable的可以:

rpm -qpi package_file.rpm | grep Relocations

或
rpm -qi installed_pkg_name | grep Relocations

比如:

rpm -qpi sqldeveloper-4.1.3.20.78-1.noarch.rpm | grep Relocations

<span style="color:red">Relocations : (not relocatable)</span>

安装一个relocatable的rpm到指定的文件夹，可以用–prefix=/blabla_path

如果你还想做的更多，还可以用--relocate OLDPATH=NEWPATH

如何制作一个relocatable的rpm，查文档吧