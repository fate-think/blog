---                                           
title: "安卓Lxc实现自动化V2"                                
date: 2023-07-18T17:31:13Z                    
draft: false                                  
---

解决 每次重启lxc需要重新执行许多指令的问题
<!--more-->

## <font color=#66CC66> 【 一 】挂载cgroup </font>
把挂载cgroup的两行命令，丢termux的.bashrc里，具体操作如下
```
$ apt install nano
$ nano ~/.bashrc
```

添加以下内容
```
sudo mount -t tmpfs -o mode=755 tmpfs /sys/fs/cgroup && sudo mkdir -p /sys/fs/cgroup/devices && sudo mount -t cgroup -o devices cgroup /sys/fs/cgroup/devices && sudo mkdir -p /sys/fs/cgroup/systemd && sudo mount -t cgroup cgroup -o none,name=systemd /sys/fs/cgroup/systemd

sudo lxc-setup-cgroups
```

&nbsp; 


## <font color=#66CC66> 【 二 】lxc里的docker网络配置 </font>
lxc运行docker前的那些网络配置，
每次重新进lxc，
那些步骤都需要重新做一遍才行，
把那些步骤，都丢rc.local即可
(lxc里的 ubuntu里的rc.local)，
不然每次都要手动搞一遍


可以在家目录即/root下，创建docker-net.sh
添加以下内容
```
#!/bin/sh
# 安卓lxc里docker配置网络
# 共7行命令
getway=$(sudo ip route get 8.8.8.8 | awk '{ for(i=1; i<=NF; i++) { if($i == "via") { print $(i+1); break; } } }')
sudo ip rule add pref 1 from all lookup main
sudo ip rule add pref 2 from all lookup default
sudo ip route add default via $getway dev wlan0
sudo ip rule add from all lookup main pref 30000
update-alternatives --set iptables /usr/sbin/iptables-legacy
iptables -t filter -F
```

然后
```
nano /etc/rc.local
```
末尾添加1行
```
bash /root/docker-net.sh
```


## <font color=#66CC66> 【 三 】lxc普通用户sudo修复 </font>
lxc普通用户不能用sudo 问题，
也是需要每次进lxc 执行1次命令 来解决，
加到 /etc/rc.local 里，让它每次自动执行，
具体操作如下

lxc容器里执行
```
$ nano ~/.bashrc
```

末尾添加1行
```
mount -n -o remount,suid /
```
