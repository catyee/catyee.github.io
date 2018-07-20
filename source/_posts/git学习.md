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

##### 获取git仓库
两种方式: 1. 在现有项目或目录下导入所有文件到Git
         2. 从一个服务器克隆一个现有的Git仓库

1. 在现有项目或目录下导入所有文件到Git
 - 首先进入项目输入 git init，此时项目里的文件还没有被追踪。
 - 如果文件夹非空,执行:
 ```
 git add *.c
 git add LICENSE
 git commit -m 'initial project version'
 ```
2. 克隆现有的仓库
   git clone [url]
 以上操作会把远程Git仓库中的每一个文件的每一个版本都拉取下来。
   git clone [url] [myResponsityName]       // 自定义仓库名
##### 检查当前文件状态
git status 
使用git status -s使输出格式更加紧凑

git add  添加内容到下一次提交中
如果git add之后重新修改了文件 需要重新git add一次 提交时才是最终修改的版本。

##### 忽略文件
创建.gitignore文件

##### 要查看已暂存和未暂存的修改
git diff 此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有暂存起来的变化内容。

##### 提交更新
每次提交前，先用git status查看是不是已经全都暂存，然后再运行git commit 命令

