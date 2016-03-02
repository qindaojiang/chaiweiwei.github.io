---
layout: post
title:  "Autolayout Animation"
date:   2015-12-03 12:03:14
categories: jekyll update
---
![setting](/assets/search_animation.gif)

我一直都感觉动画是我的一个薄弱点，因为现在都是用Autolayout来布局，要做动画之前的方式使用frame已经不生效了，现在在做国家攻略的时候要做一个随着scrollerView滑动，搜索栏变形的动画，如果需要pop动画的话是可以用 `POP`做，但是我感觉从椭圆变形成圆形，原点不变，就是frame的width在变化，没有必要用pop，之前在尝试利用Autolayout做动画一直失败的原因是，无论我怎么变化约束，对象view都不变化，这次终于成功了，也找到了我一直以来的问题，先上代码：

```objc

[self.searchButton mas_updateConstraints:^(MASConstraintMaker *make) {
            make.width.and.height.mas_equalTo(100);
        }];     
        [UIView animateWithDuration:3 animations:^{
    [self.view layoutIfNeeded];
}];

```

这个理解是通过update button的宽约束来做一个3s的动画，[self.view layoutIfNeeded];是一个重点，包含两个知识点:

1. Autolayout中容易混淆的地方：setNeedsLayout、layoutIfNeeded、layoutSubViews：[很好的参考资料](http://www.vienta.me/2015/05/18/AutoLayout-浅析动画（III）/)

2. 在autolayout下，主要是利用约束，动画的本质实际上是从一种约束状态变成另一种约束状态，从而来达到动画的目的。button转换过来的frame是相对于self.view，所以是self.view中有约束变化的时候，重新绘制自动布局。

我之前的问题就是一直是以self.button为对象在更新约束的，现在想来都在做无用功。


