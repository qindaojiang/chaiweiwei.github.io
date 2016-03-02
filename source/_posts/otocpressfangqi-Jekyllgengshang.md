---
layout: post
title:  "otocpress放弃  Jekyll跟上"
date:   2015-12-03 14:02:14
categories: jekyll update
---
### otocpress 填坑
之前blog关闭了一段时间，一位otocpress搭建的环境坏了，一直generate 失败，多少有点打击积极性的，后来一直在写还是不写中纠结，前几天一直在网上搜罗修复问题，因为mac系统升级EI，有一些环境需要更新，这边有一个比较大，也是经常出现的问题，因为网上能搜罗到（哈哈哈）：

1.octopress Errno::ENOENT: No such file or directory - compass:
这个问题找了好久，我这边问题出现的原因是ruby需要更新成2.2.3，`ruby --v`显示仍然是2.2.0

解决方案：

    brew update
    rvm install ruby-2.2.3

重新查看`ruby --version`

```
ruby 2.2.3p173 (2015-08-18 revision 51636) [x86_64-darwin14]
```

这里提供了一个[参考链接](http://schalkneethling.github.io/blog/2015/10/16/errno-enoent-no-such-file-or-directory-jekyll-octopress-el-capitan/)但是它提供的参考命令我这边部分没有效果，都是一点点折腾。

当`rake generate`成功后好兴奋啊，`rake preview`查看本地4000，完美，但是一个坑又来了，当我导入之前写的blog的时候，出现了一个大bug，导致我之后还是选择Jeklly：

2.Pygments can't parse unknown language:

有的人说是因为python要更新成3.5最新，有的人说当python版本是3.＊的时候会出现这个问题，要将python降成2.＊,反正就是有各种的问题，导致我的otocpress环境还是搭建不成功，不知道是这几天的杭州雾霾还是因为要修复blog让我头重脚轻的。

### Jekyll环境搭建

Jekyll安装起来是很简单的，但是当我将源文件上传github的时候，发现`Jekyll preview`预览时OK的，但是http://chaiwiewei.github.io总是404，当我仔细检查提交记录的时候发现就是没有index.html文件，没有首页。真的好奇怪，一直没有找到原因，我用了sourcetree工具也试了，命令的方式 `git add`也试了，index.html在目录下就是不能提交上，像一个幽灵文件一下，好吧，一定要强制add `git add -f index.html`,结局是好的，就是不知道是什么原因引起的。现在正在紧锣密鼓的将记录在本地的问题整理到blog上呢。。。。。。



