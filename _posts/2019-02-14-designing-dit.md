---
layout: post
title: "Designing the DIT"
permalink: /designing-dit/
categories: ldap dit
date: "2019-02-14 14:50:21 +0800"
---

* TOC
{:toc}
Designing the DIT

## Best Practices

This is actually a very important step and you can spend the rest of your life designing your DIT. People even write books about the subject. There are some generally accepted practices that we have [documented](http://www.zytrax.com/books/ldap/apa/structure.html) but they may not work in your specific case. Take them for what they are - a starting point for your own thinking on the subject.

We have elected to follow the classic [RFC 2377 format](http://www.zytrax.com/books/ldap/apa/ldap-root.html) and use a dc format for the DIT [root](http://www.zytrax.com/books/ldap/apd/index.html#root).

We will also organise the address book hierarchy under **people** and use an **ou** attribute for department. So we will end up with a DIT structure that looks like this:

![DIT Structure](http://www.zytrax.com/images/ldap-sample-1.gif)

## Obtain an Enterprise OID via IANA

follow this [page](http://pen.iana.org/pen/PenApplication.page) to issue an Private Enterprise Number (PEN) Request

PS: There is no fee for obtaining a PEN assignment.

[Query Registered Enterprise PEN](https://www.iana.org/assignments/enterprise-numbers/enterprise-numbers)