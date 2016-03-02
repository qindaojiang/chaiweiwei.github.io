---
layout: post
title: "UIButton ImageEdgeInsets TitleEdgeInsets"
date: 2015-09-12 16:21:27 +0800
comments: true
categories: 
---

因着抗战胜利70周年，愉快的放了三天的假，紧接着来的就是连上7天班的消息啊，因为是单休。明天预示着我们也是7天“抗战”胜利啊，明天终于可以睡一个大头觉了。今天在等后台的定位商品的接口，所以今天就“忙里偷闲”把测试提的细节完善点做好了，顺便加了一个关于订单的设计修改。

<!--more-->

我在修设计效果的时候，重新思考了怎么实现图片和文字的组合展示效果，之前看到有关联的左图片与右问题的时候，一定使用UIButton系统提供的image和title来实现，当看到右图片、左文字![setting](/assets/more_icon.png)的时候心里还是有点小犹豫的，每次用ImageEdgeInsets或者TitleEdgeInsets属性的时候位置都不正确，我通常都是用最笨的办法，将image独立出来，再做一个UIImageView，这次有时间就好好的搜了一下这个解决方案，其实还是我UIEdgeInsetsMake的时候没有算好，总是差image的一个宽度。下面是能实现效果的一个方案：

```
//图片20px 20px
CGSize size = [self.MoreButton.titleLabel.text sizeWithAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:13.0f]}];
[self.MoreButton setImageEdgeInsets:UIEdgeInsetsMake(0, size.width, 0, -size.width)];
[self.MoreButton setTitleEdgeInsets:UIEdgeInsetsMake(0, -20, 0, 20)];
```

 其实一开始我脑子里面的结构变化一定是这样的：

```
[self.MoreButton setImageEdgeInsets:UIEdgeInsetsMake(0, size.width, 0, 0)];
[self.MoreButton setTitleEdgeInsets:UIEdgeInsetsMake(0, -20, 0,0)];
```

但是如果仔细的分析过的话会发现只有UIEdgeInsetsMake(0, -40, 0,0)]的时候，title偏移图片的一倍宽度才是正确的效果，所以这里很明显啊，我猜是当UIButton的image的时候title已经偏移了20了，所以为了回到0的位置，必须要偏移－40。

这里我为了方便些了一个简单的demo，我感觉我之后再处理这个问题的时候还是会糊涂的哈，所以还是拿事实说话吧，就弄个实例，方便调试啊。
[github地址](https://github.com/chaiweiwei/EdgeInsetDemo)