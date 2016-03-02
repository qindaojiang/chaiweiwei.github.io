---
layout: post
title: "8月20日 七夕"
date: 2015-08-20 10:42:14 +0800
comments: true
categories: 
---

一、瞎说

这几天就啥折腾一点`ReactiveCocoa`和`vim`，这个看看，那个看看，vim练手的时候不熟练,还把求攻略项目中pod的一个文件弄坏了呢，还好重新`upadte`就好了，不然就尴尬了，vim真的很不好用啊，初学者就够纠结一阵子了，现在就想着到实践中学习，之前用工具写日报，这次试一下用vim吧，现在最熟练的就是：`wq`和那两个模式了，haha。下面的链接是在网上看的`vim`的资料，感觉写的很好啊，正在学习中。

```
http://coolshell.cn/articles/5426.html
```

这里一定要推荐一个`ReactiveCocoa`的学习教程，虽然没有覆盖`ReactiveCocoa`的完整知识点，它是以twitter的搜索功能在实践`ReactiveCocoa`的一些特性，觉得很浅显易懂啊，之前第一次在看`ReactiveCocoa`的时候还是很迷茫啊。

``` 
http://www.cocoachina.com/ios/20150123/10994.html
```

<!--more-->

二、想好了在敲

最近的需求印象比较深刻的就是写那个提示评论Alert和个人主页登录活动提示了，暂时效果是不难实现的，就是那些内容，主要是我的思维还没有上升一个高度啊，只知道要实现设计说的效果，没有把通用性考虑进去，如果这次写死了新手登录领30元优惠券，那下次有新的活动，就必须重新上一次，包括那个提示评论，这里在师父分析需求的时候，说可以在后台做数据的工作，客户端还是做展示的工作就可以了，这样我们就只要在后台控制活动内容的传输字段就可以在客户端展示不同的活动，客户端不用再次开发了，我感觉我还是比较死脑筋的，这一点要好好的反省，要考虑到后续的拓展。

首先我要做的就是设计好后台需要提供的字段，这个在写代码完成效果的时候会不断的添加或者删除字段，我最“擅长“扣细节了，haha，在做个人主页登录活动提示的时候，有一个数据结构没有设计好，文本的一些属性字段应该例外再封装起来，我之前就还是把他们和其他的字段排排放的，这时候合适的数据结构就显得很有优势，这是我在检查我代码的严谨性的时候发现的，一种情况，当是纯文本，没有高亮等富文本展现的时候，因为我的代码中没有针对这个做一些nil值判断，这样是会crash的，毕竟不能保证每条活动展示的时候都会有高亮什么的，当我讲文本属性再次封装的时候，这个优势就体现出来了，不用一个个的都去判断高亮的颜色、字体、起点和长度了，直接判断有没有属性这个字典key就好了，完美啊。我还是太年轻啊。

三、细节处理

当时在做提示评论Alert的时候，设计有提一个需求，除了点击取消选项能关闭浮层外，点击黑色浮层也能关闭，因为使用的是系统的UIAlertView，好像没有针对监听浮层动作的操作，所以还是靠Google啦，最近自己的“搜索小雷达”渐长啊，其实解决方案就是添加一个手势

```

......
UITapGestureRecognizer *recognizerTap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleTapBehind:)];
    
[recognizerTap setNumberOfTapsRequired:1];
recognizerTap.cancelsTouchesInView = NO;
[[UIApplication sharedApplication].keyWindow addGestureRecognizer:recognizerTap];
.......

- (void)handleTapBehind:(UITapGestureRecognizer *)sender {
    if (sender.state == UIGestureRecognizerStateEnded){
        CGPoint location = [sender locationInView:nil];
        if (![self.alertView pointInside:[self.alertView convertPoint:location fromView:self.alertView.window] withEvent:nil]){
            [self.alertView.window removeGestureRecognizer:sender];
            [self.alertView dismissWithClickedButtonIndex:0 animated:YES];
        }  
    }  
}

```

这里有一个小重点`recognizerTap.cancelsTouchesInView = NO;`,这个英文好理解，就是当有手势在View中，除了处理手势指定的动作外，会取消动作的传递，当YES的时候就表示alert中的选项按钮会收不到动作，只有NO的时候浮层的动作和选项按钮的动作才能都操作。担忧一个小Bug,就是当点击选项卡除按钮外的区域也会关闭浮层。

以上的内容均摘自`http://blog.csdn.net/lvxiangan/article/details/45016395`网站.



