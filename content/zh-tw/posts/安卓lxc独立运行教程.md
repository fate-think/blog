---
title: "安卓Lxc独立运行教程"
keywords:
- lxc
- 安卓
description: "lxc在安卓系统上单独运行，脱离termux"
date: 2023-07-20T22:45:42Z
draft: false
---

lxc在安卓系统上单独运行
<!--more-->
日期 2023年07月20日 <br>
时间 23:12

by redblue(弗朗西斯科)

&nbsp; 

## <font color=#66CC66>【 一 】 前期准备 </font>
( 1 ) <br>
mt管理器，进入手机目录/data，<br>
新建 lxc 文件夹 <br>

下载 秋秋 静态编译的 lxc文件，<br>
原下载地址 (下载后不用改名) <br>
[点这里](https://qiuqiu233.top/d/linux-deploy/lxc/lxcnew.tar.gz) <br>
备用地址 (下载以后把后缀名.zip 删掉) <br>
[点这里](https://redblue.lanzouk.com/iBUKk131g9ef)

然后把 lxcnew.tar.gz文件复制到/data/lxc, <br>
打开mt管理器，点左上角 三条杠，<br>
终端模拟器，如果是第一次使用，<br>
等它初始化一下。<br>
开始执行命令
```
$ su
$ cd /data/lxc
$ tar -xvf lxcnew.tar.gz
```

( 2 )
——检测cgroup版本<br>
分别执行以下命令
```
$ mount | grep cgroup2 
$ mount | grep cgroup1
```
如果第 1 条命令有输出信息，你就是cgroup2 <br>
如果第 2 条命令有输出信息，你就是cgroup1 <br>

如果你是cgroup1版本，执行 (执行1次即可，以后每次启动 无需再次执行)
```
$ echo "lxc.init.cmd = /sbin/init systemd.unified_cgroup_hierarchy" >>; /$PREFIX/share/lxc/config/common.conf
```
如果你是cgroup2版本 执行 (执行1次即可，以后每次启动 无需再次执行)
```
$ echo "lxc.init.cmd = /sbin/init systemd.unified_cgroup_hierarchy=0" >> /$PREFIX/share/lxc/config/common.conf
```

( 3 ) <br>
完成 cgroup和网络的一些设置<br>
( cgroup1 和cgroup2都要执行 ) <br>
( 共2条命令，只需要执行 1 次 ) <br>
```
$ echo "lxc.init.cmd = /sbin/init systemd.unified_cgroup_hierarchy=0" >> /data/lxc/share/lxc/config/common.conf

$ sed -i 's/lxc\.net\.0\.type = empty/lxc.net.0.type = none/g' /data/lxc/etc/lxc/default.conf
```

&nbsp; 

## <font color=#66CC66>【 二 】 创建和启动 lxc容器 </font>
接下来，你可以选择自己喜欢的app进行操作，<br>
例如 <br>
mt管理器 <br>
juicessh <br>
termius <br>
termux <br>
本地adb shell <br>
远程adb <br>


( 1 ) 配置环境 <br>
以mt管理器 为例，<br>
打开mt管理器，点左上角 三条杠，<br>
终端模拟器，如果是第一次使用，<br>
等它初始化一下。<br>
开始执行命令
```
$ su
$ cd /data/lxc
$ source env.sh
```

( 2 ) 创建和运行 lxc容器 <br>
创建lxc容器
```
$ lxc-create -t download -n ubuntu -- --server mirrors.tuna.tsinghua.edu.cn/lxc-images
```

以 ubuntu 23.04 为例,依次输入<br>
ubuntu <br>
lunar <br>
arm64 <br>

等它下载解压完 <br>

启动容器 <br>
```
lxc-start -n ubuntu
```
改密码
```
lxc-attach -n ubuntu /bin/passwd root
```
然后输入密码<br>
再次输入密码<br>
密码改好了!

停止容器
```
lxc-stop -n ubuntu
```

启动并进入容器
```
lxc-start -n ubuntu -F
```
输入用户名 root <br>
输入密码 你改的密码<br>

执行
```
apt update
```
发现网络有问题，失败

解决网络问题
( 共3条命令 )
```
$ groupadd -g 3003 aid_inet
$ usermod -g aid_inet _apt
$ echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

执行
```
apt update
```
发现成功了


&nbsp; 

## <font color=#66CC66>【 三 】 ubuntu后续使用 </font>
( 1 ) 更换为清华大学源 可选<br>
备份原镜像源
```
$ cp /etc/apt/sources.list /etc/apt/sources.list.bak
```
ubuntu 23.04更换清华大学源<br>
共4条命令
```
$ echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ lunar main restricted universe multiverse" > /etc/apt/sources.list

$ echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ lunar-updates main restricted universe multiverse" >> /etc/apt/sources.list

$ echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ lunar-backports main restricted universe multiverse" >> /etc/apt/sources.list

$ echo "deb http://ports.ubuntu.com/ubuntu-ports/ lunar-security main restricted universe multiverse" >> /etc/apt/sources.list
```

( 2 )
ssh连接
```
$ apt install openssh-server
```

修改ssh配置文件，允许root用户的ssh连接，
```
$ apt install nano
$ nano /etc/ssh/sshd_config
```
找到这行 <br>
#PermitRootLogin prohibit-password <br>
在它的下面添加1行 <br>
PermitRootLogin yes <br>

保存并退出 <br> 
<kbd>Ctrl</kbd> + <kbd>o</kbd> <br>
<kbd>Enter</kbd> <br>
<kbd>Ctrl<kbd> + <kbd>x</kbd> <br>

重启ssh服务
```
$ systemctl restart ssh
```

( 3 )
改时区
描述: 日期时间不对，需要改下时区

执行
```
date
```
发现日期时间不对

执行以下命令
( 即设定时区 让它自动校正时间 )
```
sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

执行 
```
date
```
发现时间自动校正了


&nbsp; 

## <font color=#66CC66>【 四 】 lxc里的docker 运行 </font>
> 参考 [安卓Lxc教程V9](https://purpleleaf.top/posts/安卓lxc教程v9/) 的以下章节 : <br>
【 六 】lxc里运行docker 
