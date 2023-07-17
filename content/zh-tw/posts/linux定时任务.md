---
title: "Linux定时任务"
date: 2023-07-17T10:19:11Z
draft: false
---

关于linux定时任务 的 笔记
<!--more-->
ubuntu定时任务

## <font color=#66CC66> 安装软件 </font>
```
apt install cron
```

## <font color=#66CC66> 启动服务 </font>
```
service cron start
```

## <font color=#66CC66> 编辑定时文件 </font>
```
crontab -e
```
- 然后会让你选文本编辑器，nano就挺好，
编辑好以后，保存并退出

## <font color=#66CC66> 重启cron使定时计划 生效 </font>
```
service cron restart
```

## <font color=#66CC66> 其它相关命令 </font>
### <font color=#605DFF> 开启cron自启动 </font>
```
systemctl enable cron
```

### <font color=#605DFF> 列出所有定时计划 </font>
```
crontab -l
```
