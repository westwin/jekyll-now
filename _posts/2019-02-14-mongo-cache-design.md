---
layout: post
title: "Cache Proposal with Mongo"
permalink: /cache-proposal-with-mongo/
categories: mongo
date: "2019-02-14 15:45:21 +0800"
---

* TOC
{:toc}
Cache Proposal with Mongo

## Overview

整个系统架构的目标是设计成<span style="color:red">"无状态"</span>的微服务，"无状态"不是说"真的没有状态", 而是说<span style="color:red">"把状态下放到集中存储中去"</span>, 通常情况下，"集中存储"包括:

* 数据库: 每次需要查库
* 分布式缓存: 每次需要查缓存

我们的系统中有少量需要经常使用的数据，比如:

* Connection的启用/禁用状态
* Password Policy
* Application列表

为了避免反复查库操作，比较好的办法是引入分布式缓存系统(比如redis).

如果不想引入分布式缓存系统，还有没有较为轻量级的变相的分布式缓存系统？

## Scenario

* 假设idp这个微服务有n个运行实例,  instance-1, instance-2 ... instance-n
* 当instance-m修改了password policy的时候，instance-n如何获得这个变更?

## Proposal

mongodb有基于[Capped Collection/Tailable Cursor的Publish/Subscribe机制](https://www.mongodb.com/blog/post/pubsub-with-mongodb), 大致思路如下：

1. 每个运行实例维护一个内存级的<span style="color:red">带TTL的LRU缓存</span>这些常用数据(不做预热，采用Lazy模式延迟加载)

2. 数据库层面维护一个叫做cache_events的capped collection, 每当password policy被instance-m修改的时候，同时写入一条变更通知至cache_events(当然也可以把更新后的数据作为一个event发送)

    ```json
    { "op_type": "mod", "target_type":"ppolicy", "target_id":"target_policy_id", "action":"reload" }
    ```

3. 每个微服务运行实例, 通过tailable cursor以<span style="color:red">阻塞/循环</span>的机制，<span style="color:red">watch</span> cache_events的变更通知

4. 当变更通知到来时，触发对应的"action", 去更新每个运行实例的LRU缓存(或者简单粗暴的把内存中对应的entry给删掉)

5. 重复上述步骤3，不停的监听变更通知