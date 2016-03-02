---
layout: post
title:  "Translucent TabBar"
date:   2015-12-03 12:03:14
categories: jekyll update
---

在修改tabbar icon的时候发现之前tabbar在translucent上的一个bug，现在新的icon底色是白色的，之前还能不理会这个问题，看上去问题不到，现在是白色的底色，问题就被放大了，在反复检查下发现是因为UITabBar的`translucent`属性造成的，现在当我将值设置为NO的时候，是能完美的解决icon的问题但是会造成一个比较严重的问题，因为app中有几个viewcontroller有`滑动动作tabbar展示的动画`，可以测试一下，当translucent=NO时，viewcontroller滑动，tabbar向下缩的时候会造成tabbar位置变成黑色背景了，tableView没有自动布局到地步，解决方案是

```objc
viewController.extendedLayoutIncludesOpaqueBars = YES;
viewController.edgesForExtendedLayout = UIRectEdgeBottom;
```

属性`extendedLayoutIncludesOpaqueBars`可以从字面上翻译成延伸不仅包括透明的bar，我在[参考链接](http://stackoverflow.com/questions/20979281/ios-7-tabbar-translucent-issue)中看到一句话：


> This happens in iOS7 when you set tabBar.translucent to NO. iOS is trying to be smart and say "hey the tabbar is not translucent so we better push everything up on top of it". Fix it by setting the extendedLayoutIncludesOpaqueBars property of the view controller inside the navigation controller which is inside the tabbar controller to YES.


翻译成:

这样的情况会发生在iOS7中，当设置tabBar.translucent为NO, iOS试图自作聪明，认为这里的tabbar是不透明的，所以我们最好将所有的东西都推到顶部，修复这个问题是将在navigation controller中的view controller属性extendedLayoutIncludesOpaqueBars设置成YES

这样做就能将我们tabbar位置上黑色背景的问题修复好了。
