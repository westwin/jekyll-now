---
layout: post
title: "企业移动门户-Gateway和SSO的集成"
permalink: /sso-gw-integration/
categories: Enterprise-Portal
date: "2018-04-02 17:48:21 +0800"
---

* TOC
{:toc}
企业移动门户-Gateway和SSO的集成: 安全接入网关和单点登录的集成方案

# GW && SSO Integration Proposal

GW做为移动端所有应用的唯一入口，GW本身也是需要身份认证才能接入的，也就意味着GW一旦身份认证成功后，
通过GW接入的应用(H5为主)，理论上不需要再重新校验身份, 即所谓的**SSO**.

## Terms

1. GW Access Token: 设备接入GW的token. 获得GW Access Token的前提是:
    - 设备身份合法: 即已激活的设备
    - 应用身份合法: 接入的应用身份合法
    - 接入User的身份合法：通常情况下为校验用户名/密码
2. Identity Token(ID Token): 即人的身份. OIDC协议中的概念，移动端SSO的流行做法. 该Token是由IDP身份校验成功后颁发. 具备几个特点:
    - JWT Token
    - RSA签名: 接入的第三方应用可以使用对应的公钥进行解签名，以验证Token的合法性
    - 包含了人的最基本的身份信息，比如用户唯一标识/用户名/邮箱/手机号等等
    - 不同于Access Token, ID Token只是表明了用户身份，**不推荐用它来做各个应用层的access token/session等**
3. Authorizer: GW中GCS里的概念，用来对接不同类型的IDP进行用户的身份校验
    - LDAP/AD
    - OAuth: `grant_type=password`
    - Allow(Disallow) All: 测试专用

## 思路

1. 用户只需登录一次
    - 即GW的登录，其他应用不需要再次登录
2. 其他应用如何识别用户身份?
    - 用户登录GW后，接入者通过GW Access Token来换取用户的ID Token
    - 其他应用的登录对接GW颁发的ID Token
3. ID Token注入到应用服务器
    - HTTP Header Manipulation
      - SDK在请求应用服务器的时候注入**X-PROXY-SSO-ID-TOKEN**
      - 当目标服务器是TLS的时候, GCS没有能力注入(无法解密层2TLS, 只能通过SDK注入)
      - 当目标服务器是**非TLS的HTTP**的时候, GCS有能力注入(层2是HTTP, 所以GCS可以注入)
    - TCP
      - 当目标应用server是非HTTP时，GW现在没有能力做.(只能是应用程序主动请求GW的接口要ID Token)

## 流程

### SSO流程

![GW SSO Flow](http://on-img.com/chart_image/5ac1ea59e4b09bf96ad632ff.png)

TBD

### 第三方应用对接SSO的改造

1. 对于HTTP/HTTPS的第三方应用服务器来说, 解析**X-PROXY-SSO-ID-TOKEN**, 从而获取当前登录用户的身份. 参见[第三方应用服务器对接SSO](https://internal-uc.nq-sky.net/help/index.html?treeNodeId=createAppSession)
2. 第三方轻应用客户端: 如果SDK注入的话，则不需要任何改动

## 目标

通过如上描述的流程， 根据Authorizer对应的那几种使用场景， **功能上**，GW可以做到:

1. **GW自己颁发ID Token**
    - AD/LDAP Authorizer: 只有校验用户名/密码的能力，IDP层面不提供任何token
    - OAuth2: 只有OAuth层面的access/refresh token, 没有ID Token
2. **GW代理SSO Server(UC)颁发ID Token**
    - 标准OIDC协议的Authorizer
    - UC(OIDC协议的其中一个实现 + 自定义的一些其他功能)
3. **GW对接客户现有的其他非标准SSO服务颁发ID Token**

模块化:

1. GW的SSO能力是可配置的，不是必须有的组件

## 对现有GW代码结构的影响

新接口:

1. 增加REST API `/sso/api/v1/token`返回当前登录用户的ID Token
    - 受限API: 即必须提供GW Access Token才允许访问
2. 增加REST API `/sso/api/v1/key`返回用于ID Token签名的公钥
    - Public API

扩展:

1. 增加对OIDC协议的Authorizer的支持
2. 自己颁发ID Token
    - 签名密钥
    - JWT
3. 代理颁发ID Token
    - 对接OIDC(UC)

数据模型:

1. 当对接的是OIDC的时候，会牵扯到一个问题，即ID Token过期怎么办？ OIDC的做法是通过第一次登录时返回的refresh token来renew一个ID Token. 参见[如何用Refresh Token更新Token](https://internal-uc.nq-sky.net/help/index.html?treeNodeId=howToRenewToken)
2. 所以需要存一下用户的refresh token
    - 服务端存储(建议, 建立GW Access Token和User Refresh Token的关联关系)
    - 或客户端存储

## 后续

1. 对接UC其他用户身份校验方式: TBD
2. Kerberos SSO的支持

