---
title: Compose
published: true
category: docker
---

Docker Compose 可以在一个单节点上部署和管理多容器应用。

### 安装
docker-compose版本地址：`https://github.com/docker/compose/releases`。
1. 使用curl安装：
```shell script
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" \
 -o /usr/local/bin/docker-compose
```
1. 赋执行权限：
```shell script
sudo chmod +x /usr/local/bin/docker-compose
```
1. 测试安装
```shell script
docker-compose --version
```

### compose file
compose文件默认名称为docker-compose.yml，可以使用`-f`指定别名。
一个简单的例子如下，指定了两个微服务（web-fe和redis）
```yaml
version: "3"
services:
    web-fe:
        build: .
        command: python app.py
        ports:
          - target: 5000
          published: 5000
        networks:
          - counter-net
        volumes:
          - type: volume
            source: counter-vol
            target: /code
    redis:
        image: "redis:alpine"
        networks:
          counter-net:
networks:
    counter-net:
volumes:
    counter-vol:
```
一级关键字
* version 指定了compose文件格式的版本，必须在第一行，是必须的。
* services 指定了不同的微服务。
* networks 告诉docker要创建的新网络。默认为桥接。
* volumes 指定docker需要创建的卷。

web-fe中的关键字
* build .表示使用当前目录下的Dockerfile创建一个新镜像。
* command 表示在容器中执行命令`python app.py`。
* ports 表示映射容器的5000端口 (-target) 到主机的5000端口 (published)。

redis中的关键字
* image 基于`redis:alpine`镜像。

### compose命令
* docker-compose up         
部署一个compose应用。默认使用docker-compose.yml或者docker-compose.yaml。建议使用`-d`参数后台运行。
* docker-compose stop：停止一个compose应用中的所有容器。
* docker-compose rm：删除一个已经停止的compose应用。这将会删除所有容器和网络，但是不会删除卷和镜像。
* docker-compose restart：重启compose应用。
* docker-compose ps：列出compose应用中的所有容器。
* docker-compose down ：停止且删除compose应用。

