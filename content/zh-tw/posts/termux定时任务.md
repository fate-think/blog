---
title: "termux定时任务"
keywords:
- termux
- 定时任务
- 自动运行
- crontab
description: "安卓手机用termux定时执行脚本，实现一些自动化部署"
date: 2023-07-17T15:37:23Z
draft: false
---

termux定时任务
<!--more-->
以定时运行python脚本为例
termux相关

## <font color=#66CC66> termux保持后台唤醒 </font>
```
$ termux-wake-lock
$ echo "termux-wake-lock" >> ~/.bashrc
```

## <font color=#66CC66>安装定时任务软件 cronie </font>
```
pkg install cronie
```

## <font color=#66CC66>开启crontab </font>
```
crond
```

## <font color=#66CC66>添加定时任务 </font>
```
crontab -e
```

加入以下代码
(例如 每天10:30执行一次python脚本)
```
30 10 * * * python3 ~/test.py > ~/test.log 2>&1
```

## <font color=#66CC66>其它 </font>
### <font color=#605DFF>定时示例 </font>
每10分钟执行 1次<br>
```
*/10 * * * *  命令
```
每月9号执行         
```
0 0 9 * * 命令
```
每年6月5日执行         
```
0 0 15 6 * 命令
``` 
每小时8分执行           
```
8 * * * * 命令
```
每周五6点执行           
```
0 6 * * 5 命令
```


### <font color=#605DFF> 命令前面的*依次代表 </font>
<font color=#486CFF>分 时 日 月 星期几 <br></font>
其中 <br>
分 取值范围 0-59 <br>
时 取值范围 0-23 <br>
日 取值范围 1-31 <br>
月 取值范围 1-12，也可以用英文jan,feb等 <br>
星期几  取值范围0-7(星期日是0或7)，也可以用英文mon,tue等

