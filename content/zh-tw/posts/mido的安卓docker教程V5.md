---
title: "mido的安卓docker教程V5"
date: 2023-07-18T13:05:15Z
draft: false
---

安卓docker教程
<!--more-->
作者 redblue (弗朗西斯科) <br>
日期 2023/06/13 <br>
时间 21:03

&nbsp; 

note4x的docker内核支持安卓11 12 12L 13 <br>
我测试过的rom有: <br>
SparkOS安卓13  <br>
ricedroid安卓12L<br>
octavi安卓11 <br>

&nbsp; 

去rec里，刷入note4x的fussion-docker内核 <br>
[内核下载地址](https://redblue.lanzouy.com/iOOGg0ph22zc) <br>
下载并安装termux <br>
[termux下载地址1](https://f-droid.org/en/packages/com.termux/) <br>
或者 <br>
[termux下载地址2](https://github.com/termux/termux-app/releases)

&nbsp; 

## <font color=#66CC66>〖 一 〗 termux 配置</font>
打开termux，更换国内源，
推荐北京大学源，执行
- 每个 $ 后是一行命令
- 以下是一行完整命令
```
$ echo "deb https://mirrors.pku.edu.cn/termux/termux-main stable main" > $PREFIX/etc/apt/sources.list
```

更新软件包列表，执行
```
$ apt update
```

添加termux的后台唤醒
```
$ termux-wake-lock
$ echo "termux-wake-lock" >> ~/.bashrc
```

&nbsp; 
&nbsp; 

## <font color=#66CC66>〖 二 〗 安装docker</font>
安装docker <br>
安装必要依赖
```
$ pkg install root-repo
$ pkg install tsu docker
```

&nbsp; 
&nbsp; 

## <font color=#66CC66>〖 三 〗 运行 docker</font>
### 1. 准备启动docker
首先手动挂载cgroup,执行如下命令
```
$ sudo mount -t tmpfs -o uid=0,gid=0,mode=0755 cgroup /sys/fs/cgroup
```

### 2. 启动docker守护进程，并让它在后台运行
这里要注意了!!!，下面的运行策略 二选一即可
如果你乱不清楚，选择第一种 <br>
<font color=#7A86B8> [ 注释1 ] 跳到文章末尾 查看注释 </font>
#### <font color=#7A86B8>第一种 </font>
```
$ sudo dockerd --iptables=false &>/dev/null &
```
#### <font color=#7A86B8>第二种 </font>
(把其中192.168.1.1改成你所在wifi的网关地址)
```
$ sudo ip route add default via 192.168.1.1 dev wlan0
$ sudo ip rule add from all lookup main pref 30000
$ sudo dockerd &>/dev/null &
```

### 3. 测试docker是否工作
  - 看到"hello world from docker"这样的提示，就表示正常运行了
```
$ sudo docker run hello-world
```

### 4. 运行具体镜像
nextcloud为例，自己类比类推
如果你乱不清楚，那就选第1种
#### <font color=#7A86B8>第一种 以host主机模式运行 </font>
  - host模式关键在于--net host
```
$ sudo docker run --net host -d --name nextcloud nextcloud:latest
```
然后在浏览器中打开 http://你的ip地址 (例如 http://192.168.9.254)
就能看到nextcloud的界面了

#### <font color=#7A86B8>第二种 以bridge桥接模式运行 </font>
  - bridge模式 关键在于-p 1111:80
  - 其中1111是端口，你随意改
```
$ sudo docker run -d --name nextcloud -p 1111:80 nextcloud:latest
```
然后在浏览器中打开 http://你的ip地址:端口 (例如 http://192.168.9.254:1111)
就能看到nextcloud的界面了

更多arm64的docker镜像，你可以通过访问这个docker官方仓库 来获取:
https://hub.docker.com/

<font color=#F8F993>教程结束！！！！ </font>
<font color=#7A86B8>建议后面的也随意看一下  </font>

&nbsp; 
&nbsp; 

## <font color=#66CC66>〖 四 〗可能遇到的问题 </font>
### 问题 1
容器内无网络
### 解决方法 1
在对应docker容器里，执行以下命令
```
$ echo "nameserver 8.8.8.8" > /etc/resolv.conf
$ groupadd -g 3003 aid_inet && usermod -G nogroup -g aid_inet _apt
```

### 问题 2
报错
```
docker: Error response from daemon: OCI
runtime create failed: container_linux.go:370: starting
container process caused: process_linux.go:326: applying
cgroup configuration for process caused: mountpoint for
devices not found: unknown.
```
### 解决方法 2
先删除对应容器，强行停止termux，再次打开termux，
在termux里执行以下命令
```
$ sudo mount -t tmpfs -o mode=755 cgroup /sys/fs/cgroup
$ sudo mkdir -p /sys/fs/cgroup/devices
$ sudo mount -t cgroup -o devices cgroup /sys/fs/cgroup/devices
```

&nbsp; 
&nbsp; 

## <font color=#66CC66>〖 五 〗细节补充 </font>
### 长话短说
每次你停止了termux，再重新打开termux，
不需要重新执行sudo dockerd命令，
直接就可以进行docker操作

只要你docker run时候用了-d参数，每次重新进入termux，
服务将自动运行

### 啰啰嗦嗦 听我解释
- 关于sudo dockerd
无论任何情况下，你停止了termux，再重新打开termux，
都不需要重新执行sudo dockerd命令，
可以直接进行各种docker操作

- 关于容器自动运行
你每次docker run创建运行新容器时，
要带上 -d即后台运行参数。
这样做了以后，当你强制停止了termux应用，下次重新打开termux，
重新打开termux，你会发现你的容器已经自动运行了。
如果要运行新的容器，直接docker run即可。
具体是这样的，
  - 你运行了memos和calibre这两个容器，
你直接强行停止termux，下次重新打开termux，
执行sudo docker ps，发现memos和calibre已经自动运行了

  - 你运行了memos和calibre这两个容器，
你用docker stop命令手动停止了这两个容器
```
$ sudo docker stop memos
$ sudo docker stop calibre
```
然后强行停止termux，下次重新打开termux，
执行sudo docker ps，发现memos和calibre没有在运行，
这时docker start命令需要手动启动

&nbsp; 

【 注释 】 <br>
【 注释1 】
- 说下区别
  - 第1种 我们后续将在host网络模式下运行容器，
host模式下无法映射端口，容器里是80端口，对应主机也是80端口
  - 第2种 我们后将在bridge即桥接模式下运行容器，
bridge模式可以映射端口，比如容器里是80端口，你可以映射到主机任意端口，
例如3333 8846 5555端口你随意，即-p 5555:80

