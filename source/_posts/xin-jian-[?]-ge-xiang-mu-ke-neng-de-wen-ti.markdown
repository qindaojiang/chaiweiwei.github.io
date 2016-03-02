---
layout: post
title: "新建一个项目可能的问题"
date: 2015-08-25 10:20:20 +0800
comments: true

---


最近本来想温习一下在storyboard中界面之间的跳转和传值问题的，毕竟这样“搭”的方式确实比代码写要快的多，没想到啊，很久没用了也生疏了，在处理界面跳转中也就连线比较熟悉了。。。。

这里先就两个问题，我在创建项目并引用过程中遇到并解决的问题：

<!--more-->

1. cocoaPods

之前在求攻略原有项目上进行新的第三方添加和使用都没有问题，安装流程是OK的，这里要注意的是当我自己创建好项目后，在原有的viewController上import头文件没有报：can not found的问题，但是当我新建控制器，并import第三方头文件的时候发现会一直报这个错误，后来也是巧合啊，我就是偏不信，之前还没有发现与“原有和新建”上的关系，多次的创建新的项目，当我发现只有在viewController上引用才是OK的时候，回头一看文件的勾选关联target上，viewController没有关联tests,但是我在创建新控制器的时候因为我安装了一个AllTarget的插件，会默认全部钩上，单纯的我没想那么多啊，就按下一步确定了，导致了后来为了找这个问题花了好多的时间。

![setting](/assets/target.png)

当我勾选掉新创建控制器与tests的关联的时候，pod中的文件就能正确的时候了。
这是为什么呢？在stackoverfloow中也能搜到蛮多类似的解决方案的，终极解决方案就如下图所示，在configuration中需要在tests中将None替换成pods，这样target不小心勾选上tests也不会出现这个问题了，想想也是厉害的，只是创建了一下项目就能遇到很多的问题：

![setting](/assets/setting.png)

2.Pch file

我记得我学习iOS的时候用的Xcode5版本，当时也是比较喜欢将一下常用的头文件导入到pch文件中，但是后来发现Xcode6就不自动创建了，说是影响build的时间，但是这样编码便利性就低了一点，一开始只有少文件的时候直接在要使用的文件中import需要的就好了，但是慢慢文件多了，重复的import写多了也是烦躁的，所以还是果断自己添加了，这里除了添加pch文件，还需要配置，这样项目才会在build的时候也去编译pch中导入的常用文件：

![setting](/assets/pch.png)

添加的选项格式为: “$(SRCROOT)/项目名称/pch文件名”

这里可以将Precompile Prefix Header为YES，预编译后的pch文件会被缓存起来，可以提高编译速度。
