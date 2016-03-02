---
layout: post
title: "8月15号"
date: 2015-08-15 13:53:58 +0800
comments: true
categories: 
---

最近相比较之前是更新的快的，haha，最近因为项目上的新需求，花了1、2天调整和测试，是一个多功能的搜索页，结构看上去是简单的，主要还是要考虑到是在之前代码上补充的功能，所以要特别的注意代码的优化，还有就是用户的搜索历史纪录，因为搜索的逻辑不同了,要做好适配之前用户的搜索记录，这里有三个细节方面的小插曲：

<!--more-->

一、UITableView's style

增加的功能是在之前的SearchController中添加的，恰好我发现从父类中继承过来的tableview的style是plain，plain有一个特性，当表有headerView和footerView的时候，他们会在表向下滑动的时候当前section的headerView会挂住，footerView也是同理，这样就麻烦了，因为这次设计中搜索出来的类型有3到4个，如果出现挂住的体验，我感觉是不太好看的，而且加上tabbar的影响，下面会讲到，footview挂住的位置会在隐藏的tabbar之上，看上去体验不好，所以我思考下，就将headerView和footerView改成了Cell，这样做要注意row的加1和减1，因为数据数组的原因，这样就不用将plain类型改成group了，不在创建新tableview的情况下。

二、Tabbar和ToolBar

求攻略中跳转页面还是复杂的，但用户使用起来是方便的，这就苦了程序猿了，像之前blog中写的关于hidesBottomBarWhenPushed的内容，这次又遇到了隐藏tabbar的问题，因为tabbar在我点击搜索结果跳转的时候会在不该显示的时候显示，因为这次还是涉及到了三个以上的跳转层级，所以用了师傅之前的方法，在需要隐藏tabbar的页面直接

```
self.tabBarController.tabBar.hidden = YES
```
一开始测试的时候发现还是能解决一部分的问题的，但是当我二次跳转到有ToolBar存在的poi内容的时候，发现ToolBar展示在离底部还有一个tabBar高度的地方，这下我就意识到tabBar.hidden 这样的方式和self.hidesBottomBarWhenPushed的区别了，所以要彻底解决这个问题,除了隐藏tabBar，还需要hidesBottomBarWhenPushed的配合，将BottomBar也隐藏了，不然会出现toolbar悬空的情况，因为这个搜索页在两个地方都出现了，但是push过来的materviewController不一样，一个有tabbar,一个没有，在第二个materviewController中不写hidesBottomBarWhenPushed时没有问题的，因为本来就是隐藏的，但是第一个materviewController不一样，我需要再做一个property，来做标记，这下能解决问题了，想到tabbar还是有点头疼呢。下面的国内博客对Tabbar和ToolBar描述还是仔细的。

```
http://blog.csdn.net/duxinfeng2010/article/details/7707960
```

三、searchBar clear

在测试的时候发现几个搜索方面的体验不太好，主要是是clear的动作不好隐藏键盘，在网上有解决方案，完美解决啊，原以为在textDidChange回调方法中也用resignFirstResponder就可以了，其实在当我们点击clear调用textDidChange的时候，searchBar还没有变成first responder第一响应者，所以又何来取消呢，只能另辟蹊径了。

```
@interface SearchViewController : UIViewController <UISearchBarDelegate> {
    // all of our ivar declarations go here...
    BOOL shouldBeginEditing;
    ....
}

...
@end



@implementation SearchViewController
...
- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil {
    if ((self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil])) {
        ...
        shouldBeginEditing = YES;
    }
}
...
@end
Later on, in the UISearchBarDelegate, we implement the searchBar:textDidChange: and searchBarShouldBeginEditing: methods:

- (void)searchBar:(UISearchBar *)bar textDidChange:(NSString *)searchText {
    NSLog(@"searchBar:textDidChange: isFirstResponder: %i", [self.searchBar isFirstResponder]);
    if(![searchBar isFirstResponder]) {
        // user tapped the 'clear' button
        shouldBeginEditing = NO;
        // do whatever I want to happen when the user clears the search...
    }
}


- (BOOL)searchBarShouldBeginEditing:(UISearchBar *)bar {
    // reset the shouldBeginEditing BOOL ivar to YES, but first take its value and use it to return it from the method call
    BOOL boolToReturn = shouldBeginEditing;
    shouldBeginEditing = YES;
    return boolToReturn;
}

```

