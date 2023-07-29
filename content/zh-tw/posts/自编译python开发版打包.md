---
title: "自编译python开发版打包"
keywords:
- python
- 源码编译
- ubuntu
description: "打包好的自己编译的最新python开发版"
date: 2023-07-29T09:30:23Z
draft: false
---

打包好的自编译的python开发版
<!--more-->
python 3.12.0b4 <br> 
arm64

&nbsp; 

by redblue( 弗朗西思科 ) <br>
日期 2023年7月29日 <br>
时间 09:39 <br>
耗时 约2.5小时 (耗时长 手机性能原因)

&nbsp; 

## <font color=#66CC66>基本信息</font>
- 简介 <br>
我编译好的python 3.12.0b4，你可以直接拿我编译好的文件，去安装

- 编译环境 <br>
vivo y69a  <br>
termux proot <br>
debian12

- 适用系统 <br>
优先ubuntu debian

- python版本 <br>
Pre-release ( 先行版 开发版) <br>
3.12.0b4 <br>

- 架构 <br>
arm64

- python源码地址 <br>
[点这里](https://www.python.org/downloads/)

- 文件名 <br>
python_3.12.0b4_arm64.tar.gz

- 文件大小 <br>
99 MB

- md5值 <br>
612e2bfdd296b7e3ff15b541e9b11b04

&nbsp; 

## <font color=#66CC66>下载地址</font>
- 下载地址1 (下载后去掉.zip后缀) ——  <br>
[点这里](https://redblue.lanzouk.com/iKbv513rdvne)

- 下载地址2 (下载后不需要改后缀)—— <br>
[点这里](https://cloud.189.cn/web/share?code=vqmEnqqAR7bi) <br>
访问码：4bcy

&nbsp; 

## <font color=#66CC66>使用方法</font>
安装必要依赖
- 这个软件，系统一般是自带的，安好以后注意看看版本`openssl version`，我们需要的是最新的3.x版本

```
apt install openssl
```

新建专用文件夹
```
mkdir -p /usr/local/python3.12
```

下载 已经打包的编译好的压缩包文件
python_3.12.0b4_arm64.tar.gz

把压缩包文件移动到 /usr/local/python3.12 目录下
```
mv python_3.12.0b4_arm64.tar.gz /usr/local/python3.12
```
```
cd /usr/local/python3.12
```
解压文件
```
tar -xvf python_3.12.0b4_arm64.tar.gz
```

删除压缩包
```
rm -f python_3.12.0b4_arm64.tar.gz
```

创建软链接
```
ln -s /usr/local/python3.12/bin/python3.12 /usr/bin/python3
```
```
ln -s /usr/local/python3.12/bin/pip3.12 /usr/bin/pip3
```

<font color=#605DFF>注意</font> <br>
> 如果你的系统里已经安了python3 pip其它版本，为了避免命令冲突，可以软链接到/usr/bin/python3.12 /usr/bin/pip3.12

```
ln -s /usr/local/python3.12/bin/python3.12 /usr/bin/python3.12
```
```
ln -s /usr/local/python3.12/bin/pip3.12 /usr/bin/pip3.12
```


查看版本
```
python3 -V
```
```
pip3 -V
```
如果你是软链接到/usr/bin/python3.12 /usr/bin/pip3.12 <br>
查看版本用以下命令
```
python3.12 -V
```
```
pip3.12 -V
```
