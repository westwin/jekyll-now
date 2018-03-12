---
layout: post
title: "Stateful Token VS Stateless JWT Token"
categories: Tech
date: "2017-06-30 22:59:21 +0800"
---

* TOC
{:toc}
Stateful Token VS Stateless JWT Token

## Stateless JWT Token

顾名思义，优点即 "无状态"， "无状态"通常意味着:
- token不需要永久保存(不用入库)
- 验证token的时候不需要查库，做必要的签名验证/有效期等验证即可
- token的payload中可以包含必要的用户信息甚至权限信息，从而不用通过查库的方式来加载用户的基本信息/权限

凡事都有两面性，因为已经颁发出去的JWT Token实际上不受你控制了，它的这些优点缺点从另一个角度来讲也意味着缺点:
- 无法revoke token
- 无法logout token
- 当用户的状态发生变化后，根据已经颁发出去的token无法检测用户的状态


## Stateful Token

顾名思义，"有状态"通常意味着如下特征:
- token需要永久保存(入库).
  - 每次登录都会有新token生成
  - token的有效期需要定时清理
  - 因为入库，会占用比Stateless Token更多的存储空间
  - 禁用/删除用户的时候，需要删除该用户的所有token
- 验证token的时候需要查库， 通常情况下根据token id来查库
- token的payload也是存在库里，通常情况下payload不包括用户的基本信息以及权限(当然不是绝对的，取决于实现)
- 当应用程序需要获取用户的基本信息或者权限的时候，往往需要再次查库，因为上述的特征

和JWT token对比，这些特征就是Stateful Token的缺点

优点也很明显，可以实现上述Stateless JWT Token无法实现的应用场景


## Design Consideration

如何在二者之间选择或者平衡？ 仁者见仁的问题.
可以先问一下需不需要实现的几个应用场景， 如果答案是YES, 那么只能选择Stateful Token了

- 当用户被禁用时，如何revoke已经颁发出去的token
- 当包含在JWT token 的palyoad中的用户的其他基本信息或者权限发生变化时，已经颁发出去的token里包含的payload变成了脏数据
- 在移动互联网时代，无法实现"从其他设备上登出"这个功能
- 用户logout时，无法禁用已经颁发出去的token
- 当用户修改密码后，是否需要revoke所有token
- 用户被删除时，无法revoke所有token

不管怎么选择，相信大家的目的都是为了尽可能的减少查库的IO操作.

不知道大家在实际项目中如何选择？
