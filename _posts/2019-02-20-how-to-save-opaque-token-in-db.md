---
layout: post
title: "How to Save Opaque Token in DB"
permalink: /save-opaque-token-in-db/
categories: token sso 
date: "2019-02-20 11:35:21 +0800"
---

* TOC
{:toc}
How to Save Opaque Token in DB

## Stateful(Opaque) vs Stateless

"有状态的token", 这里说的"有状态"通常是指,

* token的消费者需要通过API调用的方式来问token的生产者： 这个token是否有效？这个token可以用来干什么?
* 而状态通常存在于数据库中，token的生产者必须得通过查库的方式验证token是否存在

而像JWT那种无状态的token， 则不同,

* token的消费者不需要API调用，通过一个和生产者共享的密钥(对称或者非对称)来验证token的合法性
* 无状态，token可以自省(自己能够解释token的含义)

除此之外，两者还有些很重要的区别，比如,

* stateful token可以被revoke, 而stateless token则不可以(只能等自然过期)
* stateless token无法当成一次性token来使用(即用完即焚), stateful token则可以

参见另一篇[Stateful Token VS Stateless JWT Token](https://westwin.github.io/tech/stateful-token-VS-JWT-token/)

## 使用场景&数据模型

广义上来讲，token的体现形式包括:

* 一次性token: 比如, 用于找回密码的token, 验证邮箱合法性的token
* 反复使用token: 比如, cookie其实也是一种特殊的token, refresh token, access token

无论是哪种应用场景的token, 其抽象的数据模型大同小异,都包括,

* token颁发时绑定的<span style="color:red">security context(上下文)</span>: 比如token绑定的用户名，邮箱，手机号，绑定的应用id等. 这点很重要，参见下文的security一章
* token的作用域：该token是用来找回密码的？还是用来验证邮箱合法性的？
* token的有效期：过期时间
* 是一次性token还是可以反复使用的token
* token的签名/加密：Authentication/Encryption
* token id, token secret, token hash

## Security

比较安全的做法是token分成四部分组成,

* token id
* token secret
* token hash
* token security context

比如一个找回密码的token，那么，

* 用户保留: token_id-token_secret
* 数据库保留: token_id以及security context, token_hash,不保留token_secret

完整流程大致如下,

1. 用户johndoe输入邮箱johndoe@example.com，发起找回密码的请求: forget_pwd_req(uid)
2. 服务端生成用于找回密码的一次性token,
    * token_id: md5sum(uuid) = 50db8e459910a487188f1fb7c42e614f
    * token_secret: md5sum(uuid)
    * 服务端生成toekn的其他组成部分,包括
        * 作用域: scope=forget_pwd
        * 有效期: exp=2018-08-15 15:00:00
        * 一次性: otp=true
        * security context: 绑定的uid=johndoe, email=johndoe@example.com
    * 同时，服务端根据步骤b的token_secret以及c中绑定的上下文，计算出token的hash，算法比如token_hash=sha256sum(token_secret+scope+exp+otp+security context).
    * 服务端入库a,c,d，不入库b
    * 服务端把步骤a和步骤b生成的token_id加上token_secret一并发送给用户johndoe
3. 用户johndoe携带token_id-token_secret请求找回密码: forget_pwd(token_id,token_secret)
4. 验证token，
    * 服务端根据token_id从库中找出该token记录
    * 然后带上token_secret用步骤2d的相同算法计算出token_hash, 和库中的token_hash进行对比
    * 同时验证token的过期时间
    * 同时验证这个token的作用域(只能用来找回密码，不能用来验证邮箱)
    * 并且只能给johendoe来找回密码，不能给Trump找回密码
5. 如果token的otp=true,则服务端从库中删除此token记录
6. 至此流程结束

## 变种

有些场景是需要用户输入token的，比如:

* pincode
* 用于登录或解锁的one time passcode

为了使用方便，通常情况下，这种类型的token都需要长度比较短，所以上面所述的比较长的token_id-token_secret的做法不太适用.

但是整体抽象流程类似，做一些小的修改即可(比如，不要token_id了，token_secret用随机算法算出比较短的利用输入的）
