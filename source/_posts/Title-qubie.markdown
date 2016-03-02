---
layout: post
title:  "Title的区别"
date:   2015-12-03 12:03:14
categories: jekyll update
---

### self.title

重中之重，之前在学习的时候都没有发现这个小细节，self.navigationItem.title，self.tabBarItem.title，self.title的区别。一般设置nav Controller的title的时候就直接使用self.title，这是完全能满足需求的，但是随着app项目的“复杂”化，比如这里需要tabbar的title是“动态”，但是当前的nav controller的title钥匙“普吉岛”，我但是也没细想啊`self.title=@"普吉岛"`不就好了，但是发现tabbar的title从动态也变成“普吉岛”，原来是因为：

```
当self.navigationItem.title，self.tabBarItem.title没有赋值情况下值和self.title一致。
```

解决方案:

```
self.title = @"Title for TabBarItem"; // TabBarItem.title inherits the viewController's self.title
self.navigationItem.title = @"Title for NavigationBar";
```

[参考链接](http://stackoverflow.com/questions/1540718/self-title-sets-navigationcontroller-and-tabbaritems-title-why)