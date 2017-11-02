---
title: linux下解压常用tar命令
date: 2015-05-04 19:55:56
tags:
---

_原谅我总是记不住_

## tar

* 格式： `tar [选项] [文件目录列表]`

* 功能： 对文件目录进行打包备份

* 选项：

```
  -c 建立新的归档文件
  -r 向归档文件末尾追加文件
  -x 从归档文件中解出文件
  -O 将文件解开到标准输出
  -v 处理过程中输出相关信息
  -f 对普通文件操作
  -z 调用gzip来压缩归档文件，与-x联用时调用gzip完成解压缩
  -Z 调用compress来压缩归档文件，与-x联用时调用compress完成解压缩
```

**再白痴一点说**

`xxx.tar.gz` 用 `tar -zxvf xxx.tar.gz`


`xxx.tar.bz2` 用 `tar -jxvf xxx.tar.bz2`


参考 [http://www.jb51.net/LINUXjishu/43356.html](http://www.jb51.net/LINUXjishu/43356.html)

___

## 一些典型的格式

转自[http://www.cnblogs.com/eoiioe/archive/2008/09/20/1294681.html](http://www.cnblogs.com/eoiioe/archive/2008/09/20/1294681.html)

##### .tar 

* 解包：`tar xvf FileName.tar`

* 打包：`tar cvf FileName.tar DirName`

 **（tip：tar是打包，不是压缩！）**

##### .gz

* 解压1：`gunzip FileName.gz`

* 解压2：`gzip -d FileName.gz`

* 压缩：`gzip FileName`


##### .tar.gz 和 .tgz

* 解压：`tar zxvf FileName.tar.gz`
* 压缩：`tar zcvf FileName.tar.gz DirName`

##### .bz2

* 解压1：`bzip2 -d FileName.bz2`
* 解压2：`bunzip2 FileName.bz2`
* 压缩：` bzip2 -z FileName`


##### .tar.bz2

* 解压：`tar jxvf FileName.tar.bz2`
* 压缩：`tar jcvf FileName.tar.bz2 DirName` 

##### .bz

* 解压1：`bzip2 -d FileName.bz`
* 解压2：`bunzip2 FileName.bz`
* 压缩：未知


##### .tar.bz

* 解压：`tar jxvf FileName.tar.bz`
* 压缩：未知 

##### .Z

* 解压：`uncompress FileName.Z`
* 压缩：`compress FileName`

##### .tar.Z

* 解压：`tar Zxvf FileName.tar.Z`
* 压缩：`tar Zcvf FileName.tar.Z DirName`

##### .zip

* 解压：`unzip FileName.zip`
* 压缩：`zip FileName.zip DirName` 

##### .rar

* 解压：`rar x FileName.rar`
* 压缩：`rar a FileName.rar DirName `

##### .rpm

* 解包：`rpm2cpio FileName.rpm | cpio -div` 
 