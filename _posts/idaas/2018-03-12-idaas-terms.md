---
layout: post
title: "IDaaS Terms"
permalink: /idaas/terms/
categories: idaas
date: "2018-03-12 21:40:21 +0800"
---

* TOC
{:toc}
Terms of Identity as a Service

# IDaas Terms

Identity as a Service的基本概念

## Multi Tenancy

1. Tenant/Namespace
2. SaaS

## Identity Provider

1. Namespace
2. Connector
    * AD Connector
    * LDAP Connector
    * RDBMS Connector
3. Sync
    * Sync Policy
      * Attribute Mapping Policy
      * Conflict Policy
          * Overwrite
          * Ignore
          * Account Linking

## Profile

### User Profile

1. User Profile Schema

2. User Profile Property
    * id
    * domain name
    * display name
    * data type: boolean, number, string, enum
    * multi-value: 是否是多值属性
    * unique: 是否唯一
    * mandatory: 是否必须
    * constraint: 其他约束条件
    * login: 是否用来登录
    * read: 属性读取策略，可为:
      * system: 只有系统可以读取
      * anyone: 所有人可以读取
      * self: 自己可以读取自己
      * admin: 只有管理员才可以读取
    * write: 属性更新策略，可为:
      * system: 只有系统可以写
      * self: 自己可以写自己
      * admin: 管理员可以写

3. User Profile Extensible Property

### Organization Profile

同User Profile

## Password Storage Scheme

1. Algorithm
2. Metadata

## Password Policy

1. Password History Policy
2. Password Complexity Policy

## Login Policy

1. Password-less Login
2. MFA or 2MFA
3. Username and Password Login
4. Authentication Extra Factors: 比如登录时校验用户证书
5. QRCode Login
6. User Profile Properties for Login

## Application

1. IDP Connector per Application
2. Login Policy per Application