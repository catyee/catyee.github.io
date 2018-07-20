---
title: git学习
date: 2018-07-20 09:24:24
tags: git
categories: 总结(git)
---
git有三种状态: 已提交(committed),已修改(modified),已暂存(staged)
已提交表示数据已经安全的保存在本地数据库中。
已修改表示修改了文件，但还没有保存在数据库中
已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
##### 初次运行git前的配置
配置用户信息
```
    git config --global user.name"your name"
    git config --global user.email "your email"
```
**如果使用了--global选项该命令只会运行一次，如果想对特定项目使用不同的用户名与邮件地址可以去掉--global运行**