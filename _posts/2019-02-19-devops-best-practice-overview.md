---
layout: post
title: "运维军规"
permalink: /devops-rules/
categories: devops
date: "2019-02-19 21:54:21 +0800"
---

* TOC
{:toc}
运维军规 & Best Practices

## 军规

* 做好备份和回滚策略，以及测试计划
* 升级过程中，尽可能的减少服务down机时间
* 数据库升级不能丢失用户数据(数据迁移)
* 升级完成后，必须要做的Sanity Tests
* 一人敲命令，一人旁观指导/纠正
* 需要一套staging环境来模拟线上的生产环境

## Best Practice

* 严禁使用个人电脑上的build来升级生产环境, 确保使用Jenkins上的正式build
* 更新部署运行在tomcat的.war程序时，严禁直接替换war展开后的某个jar文件，确保使用Jenknis上的完整的.war文件进行更新
* 当数据库需要升级时，确保schema升级的同时，务必要能够保证用户数据的迁移，严禁丢失用户的数据/配置
* 当需要做文件备份时，务必保证文件/文件夹的权限也被备份,以及软链接的备份. 做系统恢复时，务必确保文件/文件夹的权限以及软链接的恢复(参见cp/tar命令的保留权限参数)
* 当需要重启服务时，务必保证运行必要的health check来保证该服务重启后能够真正提供服务
* 谨慎使用root帐号，谨慎使用rm -rf 等危险操作
* 严禁在命令行内直接暴露密码，比如严禁mysql -u root -ppassword. 这样会在history里留下密码记录。推荐使用mysql -u root -p，让命令行自动提醒用户输入密码
* 严禁在生产环境随意运行gcc,yum update等编译程序,升级操作系统的危险动作
* 最好每个服务都提供一种机制来返回[当前服务的semver](https://semver.org/)(或git commit号). 推荐机制包括:
  * 服务启动的时候log文件里打印版本号
  * 单独提供额外的命令行或者http api来返回版本号
* 系统升级之前要有明确的计划，步骤，文档，备份/回滚计划，sanity test计划

## What is zeus node

These "zeus" hosts:

* behave like admin or "god" for the pool of hosts they administer. The zeus nodes themselves are not exposed to the internet and are not on a load balancer.
* have write access to the shared NFS location where applications run from, this allows updates to be driven and deployed from these zeus nodes
* have (non root)  ssh keys to the hosts they manage so that maintenance commands can be remotely and securely executed. 
* in production periodically fetch log files from the individual hosts and put them into a shared archive location for simplified/centralized access
* Since these nodes do not provide customer services they "can" be used to run singleton upgrade scripts which we may need to execute for an upgrade maintenance but would rather not install on application hosts themselves, 