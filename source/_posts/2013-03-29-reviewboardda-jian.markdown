---
layout: post
title: "ReviewBoard搭建"
date: 2013-03-29 16:39
comments: true
categories: 
---

之前在Mac上折腾了一上午，最终因为包依赖的问题而放弃，转而投向了Ubuntu,因为apt-get实在是太好用，省去了许多包管理带来的麻烦
不过在Ubuntu上安装也不会很省心，前面一路顺风，当执行'easy_install ReviewBoard' 时候总是卡在了安装pytz上面，提示访问http://pytz.sourceforge.net超时，但ping的时候又显示okay,所以怀疑是读取到下载地址后的下载超时，总不能在一棵树上吊死，所以只能先手动装上pytz,执行easy_install pytz完成之后继续easy_instal 

