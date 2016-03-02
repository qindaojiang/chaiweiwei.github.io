---
layout: post
title:  "第三方App切换"
date:   2015-12-12 12:03:14
categories: jekyll update
---

我们现在的求攻略系列的app就有64个，现在有新增的离线地图和三个国家攻略，国家攻略主要是我在制作，其实也就是在本来的基础上增加target，要小心注意的info文件和URL type的信息修改。我就在这里又不小心跌了一跤。

我们的app有一个分享跳转的问题，主要出现在tencent的sdk上，当分享切换的时候不能准确的回到原先的app，这里设置了几个URL type，外部可以通过设置的scheme来启动app，拿新浪和QQ来说，这两个主要用在了第三方登录上：


name | URL type
-----| -------------
sina | sinaweibo+Bundle name
qq   | tencent+(key)100274236

上面列出的规则就能发现新浪的标识肯定是不会出现跳转混乱的问题的，但是QQ的URL，因为我们分攻略的数量太多了，针对一个app去申请1个可以好像有一点点不合理呢，所以这个key可能是4、5个app共用的。

为了解决这个问题，解决方案就是当是QQ登录的时候使用web版，不跳转到QQ App,新浪的第三方登录就没有关系，但我在创建韩国、日本、泰国攻略三个国家攻略的时候没有去设置info.plist文件，以为target是copy过来的，新浪的scheme还保留了：sinaweibo＋(old).

分析问题才能解决问题啊。
