---
layout: post
title: "新年开篇日"
date: 2016-02-23 16:03:14
comments: true
categories: 
---

这个年过的不开心，刚好也来了个“开年红”，年前比较忙，到了一个新公司，本来是想着先熟悉一下代码，需求还不需要跟上，但是好像内容堆的比较多，同一个iOS小伙伴每天加班加点的，我也有抓紧步伐跟上了，希望年前能提一版，因为iOS小伙伴是远程办公的，我感觉交流还是有一点不习惯，为了快速完成需求，并没有很仔细的阅读VC，找到重点就开始写了，好在还是年前完成期望的目标了，但是其中还是有很多不安因素，因为我们两个iOS开发水平差不多（哈哈），遇到问题要自己花相对较长的时间去查找，如果下班review前还没有解决好问题，想想也是有点小紧张呢。

之前有一个社区分享的需求，在原有基础上增加一个QQ空间的分享,之前的代码在使用第三方的时候很多都是自己手动配置的，没有使用cocoapod，因为之前的社区分享SDK用的是shareSDK，看上去已经是很久之前的版本了，现在要重新更新SDK，想想也是有点小紧张呢，因为用的是简洁版的，官方也建议使用简介版的，等替换build成功，老的shareSDK的很多方法基本都被替换了，坑爹的来了：
 
1. QQ的开发者账号找不到了，只有key，没有秘钥，之前在记录的时候只记了key，这样使用shareSDK帮我集成好的方式就不使用与QQ第三方登录了，因为shareSDK的第三方登录需要两个参数，分享只需要一个，但是使用QQ原生的方式是不需要的，除了分享，第三方登录时使用原生的方式解决的。
2. 跳转后发现跳转不会客户端，iOS9.0一下是可以的，但是iOS9以上会收不到回调，检查了很多，花了很长时间，也检查了error message，后来是因为我忽略了一个很重要的message：**retryHandleOpenURL**，当我使用客户端跳转登录成功后，回调失败就回有这个问题，具体是看了这一片[blog](http://www.jianshu.com/p/3ce4561ae7be),想我没有去看SDK更新习惯的（是个不太好的习惯，有点被动），发现有个接收回调的方法在新的SDK中被替换了

```objc
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(nullable NSString *)sourceApplication annotation:(id)annotation NS_DEPRECATED_IOS(4_2, 9_0, "Please use application:openURL:options:") __TVOS_PROHIBITED;
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary*)options NS_AVAILABLE_IOS(9_0); // no equiv. notification. return NO if the application can't open for some reason****
```

3. 微信分享失败这个是K.O了我啊，就他比较特别，因为我们的内容图片可能比较大，超过了最大的图片限制，后台没有区分小图和大图的字断，分享的时候携带的都是大图，之前开发者在写图片压缩的方法的时候只是缩小了图片的尺寸，像素值没有改变，大小还是一样的，内容可能远远大于限制大小了，但是我想吐槽的时候，微信平台分享失败（图片原因）没有任何提示，也不会跳转到微信客户端，就是没有反应，这样不是很揪心吗！而且搜不到没有任何的官方文档说具体的分享图片大小限制是多少。。。。。这个是shareSDK提供的方法，没有使用微信原生方式！

虽然好像是两个人可以讨论，但是我感觉效率还是有点低，虽然遇到问题自己找到解决方法的时候很爽，除了在上新版本遇到的各色各样的问题，还是一个要吐槽的，我感觉这边的git使用没有很规范的感觉，使用的时候多少有点不适应。

年后除了新版本的上线事宜，要分析用户使用情况，降低crash率，之前没有使用过专门的统计工具bugly，我感觉使用还是可以的，但是还是感觉信息有点少，但是靠这个工具基本能解决百分之70左右的问题吧，争取把crash降低到0.2-0.5%左右吧，但是之前因为一个8.1版本的问题，导致了crash上升到了2.5%以上，宏观来看我感觉还是OK的，但是涉及到的用户还是多的，这个问题表现在有需要截图windows图像绘制的时候

```objc
[window.layer renderInContext:context];
```
网上的资料说**renderInContext**方法有几率会crash，但是在8.1系统下百分之白会crash，其他的系统（没有测全部）重现不出来，不知道是不是有这个问题。解决方法是用新方法代替：

```objc
[window drawViewHierarchyInRect:delegate.window.bounds afterScreenUpdates:NO];
```

完美解决问题，**drawViewHierarchyInRect**会比**renderInContext**方法执行时间慢一点点，但是我们肉眼感觉不到几毫秒的差别。等新版本上线，在观察crash有没有下降。[相关的参考链接](http://newtips.co/st/questions/33592307/what-is-expected-performance-of-drawviewhierarchyinrect-vs-renderincontext.html)
