---
title: "安卓Lxc教程V10"
keywords:
- 安卓
- lxc
description: "安卓系统上运行lxc教程"
date: 2023-07-18T17:30:42Z
draft: false
---

安卓运行lxc + docker教程
<!--more-->

日期 2023年07月21日 <br>
时间 12:59

by redblue(弗朗西斯科)

基于[酷安教程](https://www.coolapk.com/feed/47288185?shareKey=OGQ2MWQxMWMxYzJhNjRhYjcyMjQ~&shareUid=3168726&shareFrom=com.coolapk.market_13.1.3)

&nbsp; 


#### <font color=#605DFF> 注意</font>
前提是你的机型已经 刷入了lxc docker内核
并且已经刷入了面具root
#### <font color=#605DFF> 注释</font>
每个 $ 后是一条命令

&nbsp; 



## <font color=#66CC66> 【 一 】安装lxc </font>
手机打开termux

一键更换北京大学源，执行
```
$ echo "deb https://mirrors.pku.edu.cn/termux/termux-main stable main" > $PREFIX/etc/apt/sources.list
```

安装lxc
```
$ pkg update
$ pkg install root-repo
$ pkg install lxc
```

&nbsp;



## <font color=#66CC66> 【 二 】关于cgroup </font>
```
$ tsu
```
——检测cgroup版本
```
$ mount | grep cgroup 
```
注意看/sys/fs/cgroup type cgroup2这行，
根据显示的信息，我的是cgroup2

如果你是cgroup1版本，执行
(执行1次即可，以后每次启动 无需再次执行)
```
$ echo "lxc.init.cmd = /sbin/init systemd.unified_cgroup_hierarchy" >>; /$PREFIX/share/lxc/config/common.conf
```

如果你是cgroup2版本 执行
(执行1次即可，以后每次启动 无需再次执行)
```
$ echo "lxc.init.cmd = /sbin/init systemd.unified_cgroup_hierarchy=0" >> /$PREFIX/share/lxc/config/common.conf
```

配置网络，cgroup1和cgroup2都要执行
(执行1次即可，以后每次启动 无需再次执行)
```
$ sed -i 's/lxc\.net\.0\.type = empty/lxc.net.0.type = none/g' /data/data/com.termux/files/usr/etc/lxc/default.conf
```

&nbsp;



## <font color=#66CC66>【 三 】挂载cgroup </font>
共2条命令
(以后每次重新启动lxc 都需要执行)
```
$ sudo mount -t tmpfs -o mode=755 tmpfs /sys/fs/cgroup && sudo mkdir -p /sys/fs/cgroup/devices && sudo mount -t cgroup -o devices cgroup /sys/fs/cgroup/devices && sudo mkdir -p /sys/fs/cgroup/systemd && sudo mount -t cgroup cgroup -o none,name=systemd /sys/fs/cgroup/systemd

$ sudo lxc-setup-cgroups
```

&nbsp;

## <font color=#66CC66> 【 四 】启动lxc </font>
```
$ tsu 

$ lxc-create -t download -n my-container -- --server mirrors.tuna.tsinghua.edu.cn/lxc-images --no-validate
```

ubuntu 22.04为例
输入
ubuntu
jammy
arm64

等待完成

到这里，先别急着启动系统，因为如果启动了 你会发现不知道登录用户名和密码

mt管理器，打开
```
/data/data/com.termux/files/usr/var/lib/lxc/ubuntu/rootfs/etc/shadow
```
这个文件，把root和ubuntu用户后的密码改一下，改为这个加密密码
paa5KD6arxLr2 <br>
[ 具体细节 ] <br>
修改密码，就是修改用户名后 2个:之间的内容 <br>
修改前 <br>
root:*:19525:0:99999:7::: <br>
ubuntu:!:19525:0:99999:7::: <br>
修改后 <br>
root:paa5KD6arxLr2:19525:0:99999:7::: <br>
ubuntu:paa5KD6arxLr2:19525:0:99999:7:::


启动ubuntu系统
```
$ lxc-start -n ubuntu -d -F
```

输入用户名 和 密码 <br>
root和ubuntu的密码都是123456

建议root用户登录

&nbsp;



## <font color=#66CC66> 【 五 】ubuntu系统后续配置 </font>

进入ubuntu系统以后， <br>
( 1 ) <br>
执行
```
$ sudo passwd root
```
可以修改root用户密码

( 2 )
更换为清华大学源 <br>
ubuntu22.04更换清华大学源 <br>
共4条命令
```
$ echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse" > /etc/apt/sources.list

$ echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse" >> /etc/apt/sources.list

$ echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse" >> /etc/apt/sources.list

$ echo "deb http://ports.ubuntu.com/ubuntu-ports/ jammy-security main restricted universe multiverse" >> /etc/apt/sources.list
```

( 3 )
修改dns:
```
$ echo "nameserver 8.8.8.8" > /etc/resolv.conf
```
但是这个文件会自动重置，也就是说安卓lxc有一个dns会自动重置的问题 <br>
解决办法 在这篇文章末尾 <br>
[ 关于安卓lxc容器dns自动重置的
解决办法 ] 

试着执行
```
$ apt update
```

如果做了以上操作，<br>
apt update 还是没有网络的话,<br>
执行以下2条命令 
- 执行第1条命令可能提示 aid_inet已经存在，那就执行第2条命令即可
```
$ groupadd -g 3003 aid_inet
$ usermod -G nogroup -g aid_inet _apt
```

执行
```
apt update
```
成功

( 4 )
ssh连接
```
$ apt install openssh-server
```

修改ssh配置文件，允许root用户的ssh连接，
```
$ nano /etc/ssh/sshd_config
```
找到并用#注释掉这行 <br>
PermitRootLogin prohibit-password <br>
然后在它下面1行添加 <br>
PermitRootLogin yes <br>
重启sshd服务
```
$ systemctl restart sshd
```

( 5 )
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


## <font color=#66CC66> 【 六 】lxc里运行docker </font>

安装docker
```
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sh get-docker.sh
```

配置路由
(以后每次重新启动lxc 都需要执行一次)
```
$ apt install iproute2

$ getway=$(sudo ip route get 8.8.8.8 | awk '{ for(i=1; i<=NF; i++) { if($i == "via") { print $(i+1); break; } } }')

$ sudo ip rule add pref 1 from all lookup main
$ sudo ip rule add pref 2 from all lookup default
$ sudo ip route add default via $getway dev wlan0
$ sudo ip rule add from all lookup main pref 30000
```

调整防火墙规则
(以后每次重新启动lxc 都需要执行一次)
```
$ update-alternatives --set iptables /usr/sbin/iptables-legacy
```

$ 调整防火墙
(以后每次重新启动lxc 都需要执行一次)
```
iptables -t filter -F
```

- 如果可以正常使用了就不需要下面的命令
```
$ iptables -t filter -X
```

[ 重点 ]docker镜像运行失败修复<br>
先下载文件runc-arm64.zip，<br>
[下载点这里](https://redblue.lanzouk.com/iENa411tn8qj) <br>
下载以后，解压，<br>
把里面的 runc 文件替换到 <br>
/data/data/com.termux/files/usr/var/lib/lxc/ubuntu/rootfs/usr/bin/ 下面

&nbsp;



## <font color=#66CC66> 【 七 】dns重置问题 </font>
关于安卓lxc容器dns自动重置的<br>
解决办法V3

具体描述:<br>
解决重启lxc会重置dns的问题<br>
解决一些docker操作会 重置dns的问题

《 一 》
如果你只是想，这一次解决当下容器问题<br>
那么手动在容器里执行，
```
$ echo "nameserver 8.8.8.8" > /etc/resolv.conf
$ systemctl stop systemd-resolved
$ systemctl disable systemd-resolved
```

《 二 》
如果你想永久解决问题，那么进行以下操作<br>
- 设置一次，永久有效
- 重启lxc也不需要 再次操作 <br>

( 1 ) <br>
如果/etc目录下有rc.local文件，继续下一步 <br>
如果没有rc.local文件，新建一个
```
touch /etc/rc.local
```

修复rc.local (修复显示condition failed)
```
$ chmod +x /etc/rc.local
```

( 2 )
```
$ vi /etc/rc.local
```
或者
```
$ apt install nano
$ nano /etc/rc.local
```


加入以下内容(一共5行)
```
#!/bin/sh
systemctl start systemd-resolved
echo "nameserver 8.8.8.8" > /etc/resolv.conf
systemctl stop systemd-resolved
systemctl disable systemd-resolved
```

保存，并退出

( 3 )
执行
```
$ systemctl enable rc-local
```
但是发现报错了
解决办法——

切换目录
```
$ cd /lib/systemd/system
```

编辑文件
```
$ vi rc-local.service 
```
或者
```
nano rc-local.service
```

在末尾空1行 ，再添加(一共3行)
```
[Install] 
WantedBy=multi-user.target
Alias=rc-local.service
```

重新执行
```
$ systemctl enable rc-local
```
会看到提示如下
```
Created symlink /etc/systemd/system/rc-local.service → /lib/systemd/system/rc-local.service.
Created symlink /etc/systemd/system/multi-user.target.wants/rc-local.service → /lib/systemd/system/rc-local.service.
```

这样就成了
再次执行
```
$ systemctl enable rc-local
```

即可

( 4 )
停止容器
```
$ sudo lxc-stop -n 容器名 -k
```
(另外要注意，强行停止termux，lxc容器并不会停止，所以还是需要用命令)

启动容器
```
$ sudo lxc-start -n 容器名
```

&nbsp;

## <font color=#66CC66> 【 八 】普通用户相关问题 </font>
( 1 )
lxc普通用户不能用sudo 解决方法
执行
```
$ mount -n -o remount,suid /
```
- 后续可以加到rc.local里，就不用每次手动执行了

( 2 )
lxc普通用户不能联网 解决方法
- 用户名填 ubuntu
```
usermod -g aid_inet 用户名
```

&nbsp;

## <font color=#66CC66>【 九 】写在最后 </font>
[ 补充 ]
( 1 )
调整lxc容器密码V5 
- 上面文章中是通过改etc/shadow文件，来改密码，我们也可以通过执行命令来改密码
- 设置容器初始root用户密码 或者忘记密码情况下 重置root用户密码
- 容器启动或者不启动，命令都有效
- 支持数字字母

改root用户密码 ，termux里执行
```
$ sudo chroot $PREFIX/var/lib/lxc/容器名/rootfs bin/passwd
```
按提示<br>
输入密码 回车<br>
再次输入密码 回车<br>
即可!  <br>

( 2 ) <br>
需要指定PUID PGID的docker镜像无法联网的问题  <br>

有的docker镜像要指定id参数， <br>
又不允许指定root用户的id,  <br>
例如heimdall alist 这类docker镜像, <br>
参数如下  <br>
```
-e PUID=1000 -e PGID=1000
```
但是这样指定普通用户id， <br>
无法连接上，网络有问题  <br>

解决办法是id指定为3003  <br>
```
-e PUID=3003 -e PGID=3003
```
