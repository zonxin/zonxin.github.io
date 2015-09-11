---
layout: linux-man
title: "linux 命令：mkdir"
author: zonxin
categories: linux命令
tags: linux shell
---

#### 名称

`mkdir` - 新建文件夹

#### 使用方法

`mkdir [选项]... 文件夹...` 

#### 详情

在文件夹不存在的情况下创建文件夹

+ `-m, --mode=权限`  
    设置文件夹权限（`权限`格式参考`chmod`命令），默认权限为 rwx-umask
+ `-p, --parents`  
    如果文件夹存在，不提示错误; 如果如果指定文件夹的父文件夹不存在则创建。
+ `-v, --verbose`  
    显示每个创建的文件夹
+ `-Z, --context=CTX`  
    (略)
+ `--help`  
    显示帮助
+ `--version`  
    显示版本号
