---
layout: post
title: "linux命令 Find篇"
modified:
categories: 
excerpt:
tags: [linux]
comments: true
author: xiexiaobo
image:
  feature: sample-image-2.jpg
date: 2015-12-03T23:39:07+08:00
---

> Find命令是linux用非常常用的命令，功能也非常强大。平时仅仅是用了下Find的几个简单的用法，并没有深入去研究高级用法。借着看leveldb的makefile的契机又研究下下Find的有关命令。特此记录一下。

## 一般格式
Find命令的一般格式为如下：

```
$ find path -option [-print] [ -exec -ok ] {} \;
```

* path: find命令查找路径
* -option: find命令选项，常见的有-name, -type, -prune等，主要用于过滤。稍后详细解释。
* -print: find命令将匹配的文件输出到标准输出
* -exec: find命令对匹配的文件执行该参数给出的shell命令。稍后详细解释。
* -ok: 和exec类似，不同的是每次执行参数给出的shell命令时会给出提示，让用户确定是否执行。

## 常见选项和用法
这节主要结合实际例子解释find命令的常见的-option有哪些，如何理解和使用，以及-print, -exec, -ok等的用法。

* -name
  
  按文件名查找文件。最常用的Find选项。比如说我想找出当前目录下所有的.h文件，命令如下:
  
  ```
   $ find ./ -name "*.h"
  ```
* -type
  
  按类型查找文件。可选type有：
  
  * b 块设备文件
  * d 目录
  * c 字符设备文件
  * p 管道文件
  * l 符号链接文件
  * f 普通文件
  
  例如：我们想查找/usr/bin下的所有链接文件，命令如下:
  
  ```
    $ find /usr/bin -type l 
  ```

* -size n

  按照文件大小查找。其中n为块的数量，每个块为512Byte。n后面可以紧接大小单位。可选的单位有：
  
  * k  kilobytes (1024 bytes)
  * M  megabytes (1024 kilobytes)
  * G  gigabytes (1024 megabytes)
  * T  terabytes (1024 gigabytes)
  * P  petabytes (1024 terabytes)

  例如，我们想找出download文件下所有大于1G的文件：
  
  ```
    $ find ~/Download/ -size +1G
  ```
  或者我们想找出当前目录下所有大小为0的空文件，并删除，命令为：
  
  ```
    $ find ./ -size 0 -exec rm -rf {} \;
  ```
* -prune

  -prune的含义是剪枝，对find匹配到的文件目录不再进入子目录查找。-prune的含义经常被误解为对匹配的进行过滤。-prune引起误解最主要的原因是因为类似这样的用法，比如我想找出当前文件夹下包含"test"之外的所有文件：

  ```
    $ find ./ -name "*test*" -prune -o -print
  ```
  注意，这个的-prune仅仅是剪枝，对匹配到"test"的文件或者目录不进入其子目录。真正过滤作用的是这里的-o，即or逻辑运算符。这里应该这么理解，当匹配到“test”的文件或者目录时，or的前半部分为真，所以不执行后半部分，即不打印匹配到的文件或目录。否则，打印出来。这样就相当于过滤掉含“test”的文件或目录。下面再举一个例子说明-prune的真正含义

  ![prune_example](http://cl.ly/image/2n2S3J1K3Y0d/Image%202015-12-02%20at%2012.26.43%20%E4%B8%8A%E5%8D%88.png)

* -atime
* -mtime
* -ctime

  这三个都是按照时间来查找。atime表示文件读取或访问的时间。mtime表示文件内容上次修改的时间。ctime表示文件状态变化的时间。和文件元数据有关的变化都会导致ctime时间的变化，比如创建到文件的链接符号，更改文件权限，移动文件等等。ctime都会发生变化。

  这些时间选项都需要与一个值n来结合使用，指定为+n,-n或者n。+n表示大于这个时间段，-n表示小于，n表示刚好等于。n后面可以接时间单位，可以指定为:
  
   * s  second 
   * m  minute (60 seconds)
   * h  hour (60 minutes)
   * d  day (24 hours)
   * w  week (7 days)
   
  例如，我想找出当前文件夹下被修改超过一天的.cc文件，可以写成：
   
  ```
    $ find ./ -name "*.cc" -mtime +1d -print
  ```
   
  或者我们想找出超过一个月没用任何改动的大于2G的大文件，并执行删除：
   
  ```
    $ find ./ -size +2G -ctime +30d -exec rm {} \;
  ```
   
  又或者我们想找出10分钟内被修改所有文件，但不包含以".doc"结尾的所有文件：
   
  ```
   $ find ./ -mtime -10m ! -name "*.doc"
  ```
   
## 与xargs结合

我们可以通过管道和xargs命令一起使用find。虽然功能和-exec类似，但是-exec命令有长度限制。如果Find出来的文件太多，使用-exec可能出现溢出错误。而xargs则是一次获取一部分文件执行。

我们给出一些常用的例子。比如用grep命令在所有"*.cc"文件中搜索“include”这个词。这个命令很有用，比如我经常需要在所有源代码里找出哪些文件出现了某个变量。

  ```
    $ find . -type f -name "*.cc" -print | xargs grep -rn "include"
  ```
再比如，我想找出当前文件中所有.git结尾的文件并删除

  ```
    $ find . -name .git | xargs rm -rf
  ```
这个命令我也常用。比方说，有个git托管的工程，我想删除这个文件夹。直接删除会弹出一堆询问是否删除.git文件的，一个个的确认让人崩溃。这时直接用这条命令可以删除git相关的管理文件。
