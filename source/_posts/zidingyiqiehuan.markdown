---
layout: post
title:  "自定义切换"
date:   2015-12-04 12:03:14
categories: jekyll update
---

昨天无意间在玩耍新版“花瓣app”的时候，发现了一个之前没有看到过的控制器切换的效果（主要也是因为自己的知识储备不够），点击花瓣的瀑布流式cell,新的控制器是从点击位置放大直填充屏幕，展示一个新的控制器界面。还有添加了一个手势，当在新界面做左右手势的时候，展示的是上一个/下一个cell详情。总的来说使用的还是挺流畅的，当时我就想也尝试一个，但是不知道是怎么实现的，其实在iOS7中已经支持了：自定义切换。我是在search一个[大神的blog](http://onevcat.com/2013/10/vc-transition-in-ios7/)的时候发现的。今天就花了点时间好好整理了一下，主要是分成两种情况和API，代码在我几近折腾的情况下提交上了[Github](https://github.com/chaiweiwei/CustomPresentationDemo)：

1.demo中只展示了对于modalVC的present和dismiss的自定义切换效果:

```
	UIViewControllerTransitioningDelegate
```

2.Navigation Controller的Push和Pop切换:

```
	UINavigationControllerDelegate
```

PS:这里要提及的是iOS8在iOS7的基础上增加了一个API：UIPresentationController，我也将其整理到项目中。


[UINavigationController Push And Pop参考链接](https://www.objc.io/issues/5-ios7/view-controller-transitions/)
