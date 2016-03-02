---
layout: post
title: "pop MJRefresh 尝试封装"
date: 2015-09-01 15:21:57 +0800
comments: true
categories: 
---

一. Pop
我们攻略app中涉及到动画的部分，比较明显的是在特殊情况下的浮层，之前做了首次发布动态的提示浮层，就是用的pop，效果使用的是scaleXY，以黑色透明的背景，一开始做的时候感觉还好，现在不知道为什么越看越有点low啊，而且大屏图片好像会有一点模糊，这次做完收藏的浮层在仔细的处理了宽度、高度适配的情况下，看上去要顺眼很多了呢，有点想把原来的动态浮层重新换掉，这里说一下我在做收藏浮层的时候遇到的问题：

<!--more-->

1. nib VS code

在看到设计稿的时候我的第一反应就是用给的切图自己写出展示的效果，当然后来发现可能让设计给几张适配好的图片会更加的方便，但是怎么说呢，折腾一下就知道哪里不足了。一开始在纯代码和nib之间犹豫啊，后来想用nib试试看，应该能简单一点的，但是我想多了，我想展示的效果是如滴滴广告类似的向下展开的效果，脑子里简单的过了一遍，说用nib，再用UIView的animation做改变frame的动画，等我自动布局完效果后发现有个很严重的bug啊，如果我要用UIView自己来实现改变height的动画的话需要使得高度从0到height的变化，但是当nib加载到界面的时候一定是有其固定宽高度的，之前再通过适配改变其宽高，就是这样，因为内部又是用autolayout写的，无论怎么样出来的效果都不尽如人意，虽然企图垂死挣扎，后来发现还是用了代码写，希望能一开始在alloc的时候能控制frame的高度并按需求堆上那几个label、imageView和button，当然这样的时候更加灵活一点，没有出现模糊和图片拉伸不正确的情况，但其中也经过了一些折腾，这里要注意的是，这里不能使浮层的宽高等比变化，试过就知道，如果是等比的话，因为浮层内部是自动布局的，当是4s小屏的时候，计算的高度不够，会将图片高度自动变小，不满足效果，所以要另外的计算高度，不能简单的用等比，我之前在修这个问题的时候还以为是我没有适配好，之后在我强行设置frame的时候发现原来是高度不够。

2. animation 

在仔细观察滴滴的动画后，想的很简单的，觉得改了frame.size.height，设置动画时间，展开的动画效果应该很好实现啊，当向下展示的时候是OK的，但是不知道为什么当height＝0的时候，实现取消上下收缩就是实现不了动画，总是瞬间就完成了，后来还是选择使用pop动画了，他的`kPOPViewFrame`能帮助实现以上的效果，选择`POPBasicAnimation`，因为需要确定的时间来控制展示和取消，只有POPBasicAnimation有`duration`属性,POPSpringAnimation需要其他控制时间的属性共同决定，因为pop会自带有淡入淡出的效果，这边我想要他线性效果，用`timingFunction`属性能解决

```

    POPBasicAnimation *animation2 = [POPBasicAnimation animation];
    animation2.property = [POPAnimatableProperty propertyWithName:kPOPViewFrame];
    animation2.toValue = [NSValue valueWithCGRect:CGRectMake(view.frame.size.width - 15.5, 29, 2, 10)];
    animation2.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear];
    animation2.duration = 0.5;

```

因为能很方便的设置fromValue和toValue，所以能帮我省略一些逻辑。

二. 封装

之前在做浮层的时候都是大片的写在`.m`中的,看上去不是很雅观啊，决定这次将代码封装一下，这里需要做四个回调block，willshow、didshow、willDismiss、didDismiss，决定将后续的一些逻辑写在block中，从而来展示浮层内容，一些show、dismiss的逻辑都通过background来做，这样看上去干净逻辑清楚.

二. MJRefresh

攻略app在6plus上一直有一个bug,UIRefreshControl在6plus大屏上endRefreshing不会隐藏掉，x需要轻轻的再下来一次，才可以，这样会有体验上的隐患，可能会挡住第一个cell内容，点击会没有响应，所以想说可以用第三方的RefreshControl，其中MJRefresh的第三方能添加一个刷新动画，加入我们求攻略的logo什么的，以为之前用到UIRefreshControl还挺多的，所以还是希望下次有刷新的展示效果需求的时候就选择用MJRefresh的看看。

![setting](/assets/refresh.png)


