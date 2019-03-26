---
title: Linux常用命令
date: 2019-03-26 17:43:16
tags:
---

linux命令行中**|**为管道输出。
### 查找
##### 一般查找
```shell
grep 关键字 文件名
```
##### 压缩文件查找
```shell
gzip -dc 文件名|grep -a -E '关键字正则'|grep...
```
##### 压缩文件输出
```shell
zcat 文件名
```
### 查看日志
##### 实时输出日志
```shell
tail -200f 文件名
```
##### 查询日志内容
```shell
grep 略
less 文件名 + /关键字
```
### 查看磁盘空间占用

```shell
df -h
```
![20190325142259-image.png](https://raw.githubusercontent.com/PennHan/YosoroImages/master/20190325142259-image.png)

##### 查看当前文件夹下各文件空间占用
```shell
du --max-depth=1 -h
```

![20190325142450-image.png](https://raw.githubusercontent.com/PennHan/YosoroImages/master/20190325142450-image.png)

##### 查看当前文件夹占用空间大小
```shell
du -sh
```
![20190325144219-image.png](https://raw.githubusercontent.com/PennHan/YosoroImages/master/20190325144219-image.png)