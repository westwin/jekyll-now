---
layout: post
title: "Token Binding"
permalink: /token-binding/
categories: token sso 
date: "2019-02-14 11:33:21 +0800"
---

* TOC
{:toc}
Token Binding

## 问题来源

张三如果偷了李四的token(bearer token), 那么张三就可以使用这个token来伪装成李四对Resource Server进行访问

问题的本质在于Resource Server不需要张三提供他拥有这个token的证明

即bearer token的特性是不需要Proof-of-Possession的一种token(见下文的基本概念定义)

那么有没有一种更安全的机制可以做到: 当张三拿着偷窃过来的token对Resource Server进行访问时，Resource Server要求张三提供证明，证明张三真的拥有这个token?

Token Binding技术(以下简称TB)就是为了解决这个问题, 而技术上，TB技术通常和Mutual TLS(mTLS)紧密相关

## 基本概念

* Bearer Token: 参见英文中关于bearer的定义

    > Keep in mind that the bearer nature of the token means whoever has possession of it can access the resource server. The bearer tokens must therefore be protected

    就跟钱一样，被人偷了，别人就可以拿去花了(相反的是信用卡，信用卡即使被偷，消费的时候通常让你提供签名来证明你拥有这个信用卡)

    JWT就是最典型的Bearer Token的一种，以及传统的opaque token也是Bearer Token

* Proof-of-Possession: 顾名思义，你拥有一样东西的证明.
* Mutual TLS

    tls协议在握手的时候，默认情况下只会客户端校验服务端的证书，而Mutual TLS(以下简称mTLS)是说服务端也需要校验客户端的证书.
    这个校验会通过TLS握手协议中的Certificate Verify来做, 即客户端要拿着它的Private Key对之前发送的TLS握手信息做个签名，服务端通过客户端的证书来校验这个签名来完成整个mTLS的校验，最终完成tls的握手流程
    经过mTLS校验成功之后，服务端可以证明客户端拥有Proof-of-Possession of Private Key

    PS: mTLS场景很多，比如Windows/IOS的MDM都会给设备颁发证书，目的是啥？就是为了mTLS

## Token Binding Overview

一句话来说，TB就是把Proof-of-Possession以某种方式associate到你的token中去. 当张三带着从李四那偷过来的token对Resource Server进行访问的时候，

除了需要提供token, 还额外需要提供Proof-of-Possession(以下简称PoP), Resource Server会根据token里associate的PoP，验证张三携带过来的PoP

只有当张三同时携带PoP + token时， Resource Server才允许访问

## mTLS(Mutual TLS) Overview

tbd

## OAuth2 mTLS

tbd
