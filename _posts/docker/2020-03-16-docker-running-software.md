---
title: running-software
published: true
category: docker
---

### 创建和启动容器
docker将运行一个软件程序所需的文件和组件集合称为一个image。     
下面，我们从Docker Hub中下载和安装一个nginx image。Docker Hub是有官方维护的可信任的image库。运行下面命令会下载、安装、启动一个nginx 
容器：
```shell script
docker run --detach --name web nginx:latest
```
然后，一行随机的字符串会写向终端。如：`7cb5d2b9a7eab87f07182b5bf58936c9947890995b1b94f412912fa822a9ecb5`。 
验证nginx 是否启动成功
```shell script
wget -O - http://web:80/
```
如果成功，显示`Welcome to NGINX!`字样。

`--detach`或其简写`-d`选项表示在后台运行，意思是，该程序虽然运行了，但是未连接到终端。通过这种方式启动很重要，因为很少有软件需要连接到终端运行。
后端运行的程序通常称为daemon或者service。daemon一般通过网络或者其他通信管道和程序或人交互。

#### 运行交互式容器
终端文本编辑器是典型的终端交互程序。通常通过输入和输出交互。docker运行交互式容器如下：
```shell script
docker run --interactive --tty --link web:web --name web_test busybox:1.29 /bin/sh
```
该命令使用两个参数--interactive (or -i) 和 --tty (or -t)。

