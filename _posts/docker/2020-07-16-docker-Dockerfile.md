---
title: docker-Dockerfile
published: true
category: docker
---

### 从Dockerfile中创建image
1. 创建Dockerfile如下：
```
# An example Dockerfile for installing Git on Ubuntu
FROM ubuntu:latest
LABEL maintainer="dia@allingeek.com"
RUN apt-get update && apt-get install -y git
ENTRYPOINT ["git"]
```
2. 构建镜像ubuntu-git:auto .
```shell script
docker image build --tag ubuntu-git:auto .
```
也可以指定Dockerfile文件名git.df。
```shell script
docker image build --tag ubuntu-git:auto -f git.df .  # 注意后面的构建路径.
```

* FROM ubuntu:latest :创建镜像的起始镜像。
* RUN 命令运行git运行前需要执行的命令。
* ENTRYPOINT ["git"] 初始点，进入镜像后执行git命令。

### .dockerignore文件
排除文件让文件不加入到image中。内容如下：排除多个df文件。
```
mailer-base.df
mailer-logging.df
```

### 命令
* COPY      
COPY指令从文件系统copy文件到容器中，如：`COPY ["./log-impl", "/app"]`。
* VOLUME        
和--volume参数相同，创建docker卷，如：`VOLUME ["/var/log"]`。
* CMD    
CMD 用于指定容器主进程的启动命令，我们直接 `docker run -it ubuntu`，默认指定的命令是`/bin/bash`。  
可指定自定义的命令，如下，使用`cat /etc/os-release`替换默认的`/bin/bash`。
    ```shell script
    docker run -it ubuntu cat /etc/os-release 
    ```
    在Dockerfile中指定的CMD指令优先级低于容器启动指定的命令，会被容器启动命令覆盖。

* ENV       
设置环境变量，语法如下：
    ```
    ENV <key> <value>
    ENV <key1>=<value1> <key2>=<value2>...
    ```
* EXPOSE    
声明容器提供服务的端口，只是一种声明。运行容器时还需要主动开启。
* WORKDIR       
WORKDIR 指令可以来指定工作目录，以后各层的当前目录就被改为指定的目录，如该目录不存在，WORKDIR 会帮你建立目录。
  
### ENTRYPOINT和CMD
ENTRYPOINT和CMD都可以用来指定容器的入口命令。ENTRYPOINT具有优先权。
ENTRYPOINT和CMD有两种语法模式：shell和exec模式。
```
<ENTRYPOINT|CMD> executable param1 param2 ...             # shell模式
<ENTRYPOINT|CMD> ["executable", "param1", "param2", ...]  # exec模式
```
如：
```shell script
ENTRYPOINT "/docker-entry.sh" "param1" "param2"  # shell模式 
CMD  [ "/docker-cmd.sh" ,"param1","param2"] # exec模式
```
docker-entry.sh内容如下：
```shell script
#!/usr/bin/env bash
# 打印脚本入参
echo "Entry of ENTRYPOINT, ARGS[#]=$#"
for ((i = 0; i <= $#; i++ )); do
  echo "ENTRYPOINT ARGS[${i}]=[${!i}]"
done
```
docker-cmd.sh内容如下：
```shell script
#!/usr/bin/env bash
# 打印脚本入参
echo "Entry of CMD, ARGS[#]=$#"
for ((i = 0; i <= $#; i++ )); do
  echo "CMD ARGS[${i}]=[${!i}]"
done
```

       
#### SHELL模式
* 如果定义了ENTRYPOINT，就会执行ENTRYPOINT，忽略CMD。
* 如果没有ENTRYPOINT，则执行CMD，如果没有CMD，则失败。

用例如下：
```shell script
FROM centos:latest
ADD ./docker-cmd.sh     /docker-cmd.sh
ADD ./docker-entry.sh   /docker-entry.sh
ENTRYPOINT  "/docker-entry.sh" "entry-shell-param1" "entry-shell-param2"
CMD         "/docker-cmd.sh" "entry-cmd-param1" "entry-cmd-param2"
```
创建镜像并运行容器
```shell script
docker image build -t shell-test:latest -f shell-test.df .
docker run --rm shell-test:latest
```
运行结果如下，可以看到，ENTRYPOINT生效，CMD忽略。     
```
Entry of ENTRYPOINT, ARGS[#]=2
ENTRYPOINT ARGS[0]=[/docker-entry.sh]
ENTRYPOINT ARGS[1]=[entry-shell-param1]
ENTRYPOINT ARGS[2]=[entry-shell-param2]
```

#### EXEC模式
* 如果ENTRYPOINT和CMD都存在，CMD的内容会被作为参数传给ENTRYPOINT。都作为ENTRYPOINT的参数。
* 如果没有ENTRYPOINT，则执行CMD，如果没有CMD，则失败。

用例如下：
```shell script
FROM centos:latest
ADD ./docker-cmd.sh     /docker-cmd.sh
ADD ./docker-entry.sh   /docker-entry.sh
ENTRYPOINT  [ "/docker-entry.sh","exec-entry-param1","exec-entry-param2"]
CMD         [ "/docker-cmd.sh" ,"exec-cmd-param1","exec-cmd-param2"]
```
创建镜像并运行容器
```shell script
docker image build -t exec-test:latest -f exec-test.df .
docker run --rm exec-test:latest
```
在运行容器后，容器获得的参数如下，可以看到CMD的内容被作为ENTRYPOINT命令的参数。
```
Entry of ENTRYPOINT, ARGS[#]=5
ENTRYPOINT ARGS[0]=[/docker-entry.sh]
ENTRYPOINT ARGS[1]=[exec-entry-param1]
ENTRYPOINT ARGS[2]=[exec-entry-param2]
ENTRYPOINT ARGS[3]=[/docker-cmd.sh]
ENTRYPOINT ARGS[4]=[exec-cmd-param1]
ENTRYPOINT ARGS[5]=[exec-cmd-param2]
```

#### 使用docker run自带的命令
容器运行自带的命令会覆盖Dockerfile脚本中CMD定义的命令。
如有如下Dockerfile脚本：
```shell script
FROM centos:latest
ADD ./docker-cmd.sh     /docker-cmd.sh
ADD ./docker-entry.sh   /docker-entry.sh
CMD [ "/docker-cmd.sh" ,"exec-cmd-param1","exec-cmd-param2"]
```
创建并运行容器
```shell script
docker image build -t test:latest -f test.df .
docker run --rm test:latest date
```
则date命令会替换CMD定义的命令，直接打印结果：
```
Thu Jul 30 08:37:00 UTC 2020
```

### ONBUILD
当该镜像被其他镜像引用时，该命令定义了构建时需要执行的指令。可使用该命令编译由下层layer提供的程序。如：
```shell script
ONBUILD COPY [".", "/var/myapp"]
ONBUILD RUN go build /var/myapp
```
ONBUILD会被作为metadata保存在镜像中，当其他Dockerfile使用FROM指令引用该镜像时，会在FROM指令后，和下个指令执行前执行ONBUILD指令。

先创建一个上游的Dockerfile，名称为upstream.df。在其中定义一个ONBUILD命令。
```shell script
FROM busybox:latest
WORKDIR /app
RUN touch /app/base-evidence
ONBUILD RUN ls -al /app
```
再创建一个下游的Dockerfile，名称为downstream.df。
```shell script
FROM zhaoyu/upstream
RUN touch downstream-evidence
```
构建上游镜像
```shell script
docker image build -t zhaoyu/upstream -f upstream.df .
```
构建下游镜像输出如下，可以看到`ONBUILD RUN ls -al /app`在FROM后立即执行。
```
Step 1/2 : FROM zhaoyu/upstream
# Executing 1 build trigger
---> Running in 591f13f7a0e7
total 0
drwxr-xr-x 1 root root 4096 Jun 18 03:12 .
drwxr-xr-x 1 root root 4096 Jun 18 03:13 ..
-rw-r--r-- 1 root root 0 Jun 18 03:12 base-evidence
Removing intermediate container 591f13f7a0e7
---> 5b434b4be9d8
Step 2/2 : RUN touch downstream-evidence
---> Running in a42c0044d14d
Removing intermediate container a42c0044d14d
---> e48a5ea7b66f
```

### 定义容器的版本
在Dockerfile定义版本命令如下：
```shell script
ARG VERSION=0.6
```
在命令中使用选项`--build-arg`：
```shell script
version=0.6; docker image build -t dockerinaction/mailer-base:${version} \
-f mailer-base.df --build-arg VERSION=${version} .
```

### 健康检查
docker容器拥有三种状态。
* 0:success-容器健康，可以正常使用。
* 2:unhealthy-容器不能正常工作。
* 3:reserved-系统保留，不能使用。

Dockerfile中使用HEALTHCHECK命令：
```shell script
FROM nginx:1.13-alpine
HEALTHCHECK --interval=5s --retries=2 CMD nc -vz -w 2 localhost 80 || exit 1
```
`||`表示或，如果命令`nc -vz -w 2 localhost 80`返回任何非0的状态，那么执行`exit 1`。

使用命令参数`--health-cmd`进行健康检查。
```shell script
docker container run --name=healthcheck_ex -d --health-cmd='nc -vz -w 2 localhost 80 || exit 1' nginx:1.13-alpine
```

