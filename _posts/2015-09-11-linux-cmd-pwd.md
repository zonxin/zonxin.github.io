---
layout: linux-man
title: "linux 命令：pwd"
author: zonxin
categories: linux命令
tags: linux shell
---

#### 名称

`pwd` - 显示当前工作目录

#### 使用方法

`pwd [选项]...` 

#### 详情

显示当前工作目录的绝对路径

+ `-L, --logical`  
    使用环境变量 PWD，即使其中包含符号链接。
+ `-P, --physical`  
    将目录中的符号链接替换为实际的路径
+ `--help`  
    显示帮助
+ `--version`   
    显示版本号
