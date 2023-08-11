---
title: "安卓 Lxc 备份教程"
keywords:
- lxc
- 备份
description: "关于安卓 Lxc 容器的备份和恢复"
date: 2023-08-11T17:13:36Z
draft: false
---

lxc备份教程<br>
<!--more-->
lxc备份演示<br>
以我的arch容器为例,你也可以直接备份/data/lxc目录，方法是一样的

by redblue(弗朗西思科) <br>
日期 2023年07月24日 <br>
时间 01:27

&nbsp; 

## <font color=#66CC66>【 一 】备份 lxc 容器 </font>
```
su
```

```
cd /data/lxc/lib/lxc/arch
```
打包并压缩文件夹下的内容
```
tar -zcvf ../archback.tar.gz ./
```
或者
```
tar -zcvf ../archback.tar.gz * .*
```

<br>

把备份文件移动出来，<br>
不然一会我们删除容器可能被清除<br>
你也可以移动到 其它路径
```
mv ../archback.tar.gz /data/lxc/
```

&nbsp; 
&nbsp; 

## <font color=#66CC66>【 二 】删除原容器 </font>
接下来我们删除 这个容器
- 要记住自己设置的用户名 密码，后续有用
```
lxc-destroy arch
```

查看是否少了一个arch容器
```
lxc-ls
```

&nbsp; 
&nbsp; 


## <font color=#66CC66>【 三 】恢复 lxc 容器 </font>
删除成功后，新建文件夹
```
mkdir -p /data/lxc/lib/lxc/arch
```

把备份的archback.tar.gz移动进去，并解压
```
mv /data/lxc/archback.tar.gz /data/lxc/lib/lxc/arch/
```
```
cd /data/lxc/lib/lxc/arch
```
```
tar -xvf archback.tar.gz
```

查看是否多个一个arch容器
```
lxc-ls
```

结果确实新出现了一个arch 容器<br>
试着启动它
```
lxc-start arch
```
输入你原来的用户名和密码，登录成功
