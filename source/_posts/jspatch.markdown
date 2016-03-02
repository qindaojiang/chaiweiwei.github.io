---
layout: post
title:  "JSPatch问题总结"
date:   2015-12-29 16:03:14
categories: jekyll update
---

至今使用JSPatch修了两三个bug了，一开始用还有点小紧张呢，写的会比较慢，发现问题后，我的处理顺序是先在oc代码中把问题解决好，这时候开始意识到自己代码封装上的缺点了，代码块时不时超过50行，函数名有待改进什么的，现在在写功能的时候都会不自觉的去检查函数有没有查过一屏，51行也扣成50行，哈哈（任性），不想给以后留坑，特别是现在接着JSPatch的便利好好整顿一下自己的代码，hotfix之后，用JSPatch Convert工具粗略的转换一下，这个工具不是百分百正确的，但是能帮助转换一些简单繁琐的代码，一些数组、字典、block什么的还是要自己好好的检查，[JSPatch的wiki](https://github.com/bang590/JSPatch/wiki)就要常翻阅了， 反正遇到要转换block的时候就要去找一找，还有一些特殊的结构体，wiki都在不断的更新当中。

这里总结一下我这几次遇到的问题：

### 动态增加property
这个知识点很重要的:[wiki](https://github.com/bang590/JSPatch/wiki/defineClass使用文档),要新增成员变量只能通过`setProp_forKey()`,并且该成员变量只能使用`getProp()`来获取，不像OC中定义的已经定义好的Property，可以直接用 `get set`方法获取，详细的可以阅读wiki中的段落，这边我遇到的问题是，我需要A类和B类push操作的时候，带一个type的类型来区分，当时是抱着测试的目的在写js的，所以只defineclass了A类型，一调试，果然是有问题的，代码姑且如下：

```objc

defineClass("A", {

  shareToChat: function() {
    var A = A.alloc().init();

    A.setDelegate(self);

    A.setModel(self.model());

    A.setProp_forKey(1,"type");

    var navigationController = UINavigationController.alloc().initWithRootViewController(A);

    self.presentViewController_animated_completion(navigationController, YES, null);
  }
})

```

我当时想的crash的地方可能还是出在了setProp_forKey上面，可能不允许我在两个类中切换，但是一定要实现这个问题，想着有公司还用JSPatch来做一整个app呢，我这个应该还是我自己的问题，`getProp()`还没写，想说等一下把defineclassA测试OK了再写B，这个就为我填坑埋下了伏笔啊。调试中一直提示找不到 `setProp_forKey（）`方法，折腾了好久，百般无奈只有求助了，在JSPatch群中求助，得到的结果是：

`js文件中只有define过的class才能使用setProp_forKey()和getProp()`，这边注意的是A没有define，JSPatch源码中有描述：

```objc

  static NSDictionary *defineClass(NSString *classDeclaration, JSValue *instanceMethods, JSValue *classMethods) {
  .......
  .......

  #pragma clang diagnostic push
  #pragma clang diagnostic ignored "-Wundeclared-selector"
      class_addMethod(cls, @selector(getProp:), (IMP)getPropIMP, "@@:@");
      class_addMethod(cls, @selector(setProp:forKey:), (IMP)setPropIMP, "v@:@@");
  #pragma clang diagnostic pop

  return @{@"cls": className};
  .......
  .......
}

```

这部分在wiki中没有提到过，我以为没有看过源码，也没有在意，很理所当然的就发生的bug了，好在在理解后顺利解决了问题


### 括号
我作为一个初学者总是被要不要()弄的晕头转向的，因为js里面不认识property属性，除了调用getter，没有办法获取property，比如：

```objc

[self model] -> self.model()

```

所有的方法 js都得加括号,.xxx()的意思就是 调用方法，但是如果model已经传值给了var，则不需要再加()

```javascript
var model = self.model()
model.name()
```

### 调试
在调试的时候通常使用safari，有一点需要注意的是，当希望debug查看值的时候，光`console.log`没有用,需要将object转换成js:

```javascript
var jsstr = ocstr.toJS()
```
不然输出都是object.object。

### 可变参数
JSPatch不支持可变参数

```javascript
var string = NSString.stringWithFormat("chat:%", self.model().groupID())
```
string是不能正确赋值的，需要使用`+`连接字符串

```javascript
var string = "hello " + self.model().name().toJS()
```
### block中self的引用

在block中声明self或者其他的强引用和弱引用比objc中严格，objc中就是warning，但是js中会影响效果的实现，该用strongSelf的时候不要再使用self。

```javascript
var weakSelf = __weak(self)
var strongSelf = __strong(weakSelf);
```
