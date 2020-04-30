---
title: 解决Mac系统Chrome Helper 占用CPU过高导致发热风扇狂转
date: 2020-04-30 10:42:27
categories: mac
---
最近使用Mac的时候突然风扇狂转，摸了摸touchbar上方有点烫。觉得有点奇怪，程序也没跑几个，打开活动监视器查看，发现 Google Chrome Helper 的CPU占用率达到104%，google以后找到解决方法。

解决方法：

[原文地址](https://apple.stackexchange.com/questions/272000/how-to-make-google-chrome-helper-not-use-as-much-cpu)

> Invoke ⋮ Chrome Menu → More Tools → Task Manager to see what exactly consumes CPU. Because the helper is a black box from the OS' side of view. I personally found a mining extension that way.

发现开showdoc占用的cpu有点高，最近升级了showdoc版本才导致这个问题，页面开着闲置久了会发生CPU使用率升高，先把网页关了，后面研究下是showdoc什么问题。