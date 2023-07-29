---
title: "miui 负一屏没加号"
keywords:
- miui
description: "老机型安装miui智能助理(即负一屏应用)，右上角没有加号，因而无法添加小部件，如何解决?"
date: 2023-07-29T10:55:26Z
draft: false
---

miui的智能助理没加号 解决办法
<!--more-->

老机型 低端机 <br>
负一屏没有自定义新版小部件的 加号 <br>
的解决方法/教程

&nbsp; 

by redblue（弗朗西思科） <br>
日期 2023年07月29日  <br>
时间 10:55

&nbsp; 

> 我很久以前写的一个教程了，现在还有人需要， <br>
就再发一下

> 基本思路是反编译智能助理.apk，修改class.dex文件里的smali代码

&nbsp; 

## <font color=#66CC66>修改 1</font>
用mt管理器打开`智能助理.apk`，点`class.dex`,用dex++编辑器打开(可以两个class文件都选上)， <br>

找到路径c/h.a/，打开a  <br>
(现在新版智能助理路径是c/g.a，打开a)  <br>

搜索`.method static constructor ()V`  <br>
改成以下的样子  <br>
```
.method static constructor ()V
.registers 7

return-void
.end method
```

保存，返回

&nbsp; 

## <font color=#66CC66>修改 2</font>
找到路径g/b/h/，打开b， <br>
搜索`.method static constructor ()V`  <br>
改成以下的样子  <br>
```
.method static constructor ()V
.registers 42

return-void
.end method
```

保存，一路保存，退出

&nbsp; 

## <font color=#66CC66>最后一步</font>
把修改好的智能助理的apk文件，改名成 <br>
`MIUIPersonalAssistant.apk`

复制到`system/priv-app/MIUIPersonalAssistant/` 这个目录下， <br>

重启手机  <br>
发现负一屏右上角有+ ,可以自由添加小部件了


