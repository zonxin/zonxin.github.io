---
layout: linux-man
title: "linux 命令：ls"
author: zonxin
categories: linux命令
tags: linux shell
---

#### 名称

`ls` - 显示文件夹的内容

#### 使用方法

`ls [选项]... [文件(夹)]...` 

#### 详情

显示`文件（夹）`的详细信息，默认为当前路径。在没有选项`-cftuvSUX`和`--sort`选项的情况下，输出按照字母顺序排序。


* `-a, --all`   
    不要忽略以 . 开始的文件
* `-A, --almost-all`   
    显示所有不包括 . 和 .. 的文件
* `--author`  
    与 `-l` 同时使用可以显示每个文件的作者
* `-b, --escape`  
    用C语言的的转义显示不可显示字符
* `--block-size=大小`    
    以`大小`指定的大小作为单位显示文件大小，也就是说，显示的文件大小将是文件实际大小除以`大小`，`大小`的格式见后面。
* `-B, --ignore-backups`   
    不要显示以 ~ 结尾的文件
* `-c`   
    如果同时给出了选项 `-lt` 则显示文件的上一次的修改时间，并且按照上一次修改时间排序; 如果同时给出了选项 `-l` 则显示文件的上一次的修改时间，但是输出按照文件名排序。否则按照最后修改时间排序。
* `-C`   
    按列输出
* `-d, --directory`  
    显示参数中的文件夹信息，而不是文件夹中的文件
* `-D, --dired`  
    以 Emacs 要求的格式输出
* `-f`  
    不对输出进行排序，相当与添加了`-aU`，指定的`-ls --color`这几个选项将会不起作用。
* `-F, --classify`  
    输出中添加一个文件类型的标志（ \*/=>@\| 中的一个）
* `--file-type`  
    同上，但是如果文件类型为 \* 则不添加这个标志
* `--format=格式`  
    使用指定的格式显示， across 相当于 `-x`;commas 相当于 `-m`; horizontal 相当于 `-x`; long 相当于 `-l` single-column 相当于 `-1`; verbose 相当于 `-l`; vertical 相当于 `-C`
* `--full-time`  
    相当于 `-l --time-style=full-iso`
* `-g`  
    相当于 `-l` 但是不显示所有者
* `--group-directories-first`  
    优先显示文件夹，如果指定 `--sort=none (-U)` 则该选项失效
* `-G, --no-group`  
    在长格式输出中不显示文件的所属组
* `-h, --human-readable`  
    与 `-l`同时使用可输出方便人类阅读的文件大小, 比如 1K,234M,2G
* `--si`  
    同上，但是使用 1K = 1000B，1M=1000K...这样的换算关系
* `-H,--deferfernce-command-line`  
    如果参数指定的是符号链接，显示对应文件的信息，而非该符号链接的信息
* `--deferfernce-command-line-symlink-to-dir`  
    如果参数指定的是指向文件夹的符号链接这显示这个文件夹的信息
* `--hide=模式`  
    不显示`模式`指定的文件信息，如果指定了`-a`/`-A`则该选项失效。
* `--indicator-style=方式`  
    使用指定的`方式`表示文件类型，默认为 none; slash 相当于`-p`; file-type 相当于`--file-type`; classify 相当于 `-F`
* `-i, --inode`  
    显示文件的 inode 号
* `-I, --ignore=模式`  
    不显示`模式`指定的文件信息
* `-k, --kibibytes`  
    使用 1024 字节的块
* `-l`  
    使用长格式显示
* `-L, --deferfernce`   
    如果要显示的文件是一个符号链接，那么显示所链接的文件信息，而不是这个符号链接的信息
* `-m`  
    使用逗号分割各个文件
* `-n, --numberic-uid-gid`  
    同 `-l` 但是显示的是用户ID和组ID
* `-N, --literal`  
    .........
* `-o`  
    同 `-l` 但是不显示所属组
* `-p, --indicator-style=slash`  
    在文件夹名后面添加 /
* `-q, --hide-control-chars`  
    不可显示字符显示为 ？
* `--show-control-chars`  
   显示不可见字符
* `-Q, --quote-name`  
    给文件名两端添加双引号
* `--quoting-style=类型`  
    `类型`取值可以是 literal,locale,shell,shell-always,c,escape
* `-r, --reverse`  
    逆序输出排序的结果
* `-R, --recursive`  
    显示子文件夹中的信息
* `-s, --size`  
    显示每个文件占用的空间，以块为单位
* `-S`  
    输出按文件大小排序
* `--sort=方式`  
    输出使用 `方式`指定的方式排序。`方式`的取值：none 相当于 `-U`, extension 相当于 `-X`, size 相当于 `-S` time 相当于 `-t`， version 相当于 `-v`
* `--time=类型`  
    与 `-l`同时使用则显示`类型`指定的时间戳，而不是修改时间。`类型`的取值为： atime 相当于 `-u`; access 相当于 `-u`；use 相当于 `-u`, ctime相当于 `-c`,status 相当于 `-c`。如果`--sort=time` 那么使用`类型`指定的时间戳排序。
* `--time-style=格式`  
    与 `-l`同时使用则用指定的`格式`显示时间，`格式`的取值可以是 full-iso,long-iso,iso,locale,+FORMAT。FORMAT 是指用字符串表示输出格式，具体参见命令 `date`
* `-t`   
    按照修改时间排序，最后修改的排在最前面
* `-T, tabsize=列数`  
    指定 tab 的宽度
* `-u`  
    如果同时给出了选项 `-lt` 则显示文件的访问时间，并且按照访问时间排序，如果同时给出了选项 `-l`则显示访问时间，并且按照文件名排序
+ `-U`  
    不需要对输出排序
+ `-v`  
    通过文件的版本号排序
+ `-w, --width=列数`  
    指定输出的列数
+ `-x`   
    按行输出
+ `-X`  
    按照扩展名排序
+ `-Z, --context`  
    .....
+ `-1` (数字1)  
    每行输入一个文件
+ `--help`  
    显示帮助信息
+ `--version`  
    显示版本号

`大小` 是一个整数加上一个可选的单位组成 (例如 10M = 10\*1024\*1024)，单位 K,M,G,T,P,E,Z,Y（1024的幂次），或是 KB，MB，...（1000的幂次）

默认情况下(或者使用选项`--color=never`) `ls` 的输出不会用颜色区分文件类型。 使用选项`--color=auto`只有在输出到终端上的时候才会使用颜色区分文件类型。 可以通过环境变量 LS_COLORS 设置输出的颜色。这个环境变量可以使用 dircolors 命令改变。

#### 返回值

+ 0 执行成功
+ 1 较小的错误，例如，无法读取子文件夹
+ 2 严重错误，例如，无法读取参数
