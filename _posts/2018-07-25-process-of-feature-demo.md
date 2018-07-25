---
layout: post
title: "Feature Demo流程"
permalink: /process-of-feature-demo/
categories: agile
date: "2018-07-18 10:58:21 +0800"
---

* TOC
{:toc}
Feature Demo流程

每个sprint结束之后，如果有可以demo的feature，需要给产品经理等相关人等做基本功能的demo，产品经理会根据demo尽可能的提出反馈。

从而快速迭代修改反馈.

## 要求

* demo的负责人： server端各API对应的开发 来负责整个demo
* demo的内容 :
     1. demo环境的搭建，需要使用共用的demo环境，即 DEMO & STAGING
     2. demo的build: 使用master分支的nightly_build, 禁止使用dev自己的环境
     3. demo的内容： 端到端的演示，使用uc console来演示各个功能要点以及可能的边缘分支
* demo的时间点:
     1. API 以及相关的console 主体功能基本完成的情况下可以demo，不能等QA完全测试结束才demo
     2. API负责人需要积极主动和相关console的开发协商demo时间

## feature可以demo的前提

* 主要API开发基本完毕
* 相关API文档基本完毕
* 相关安装部署脚本基本完毕
* 相关console开发基本完毕
* 相关API/console集成测试基本完毕， 主要这里测试说的是开发自测，不是说QA的测试

## QA可以拒绝测试的前提

测试不仅仅是QA自己的事情，也是每个开发的事情。当一个功能提测给QA后，如果QA发现如下情况可以拒绝继续测试，可以打回去让相关开发自己做好测试之后再提测:

* 相关API后台开发没有和console开发做过端到端的集成测试
* 质量非常差，端到端的基本功能都不过
* 这个解释权归QA所有， QA有权利自己决定要不要拒测