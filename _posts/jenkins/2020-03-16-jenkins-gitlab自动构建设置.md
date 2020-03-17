---
title: jenkins-gitlab自动构建设置
published: true
category: jenkins
---

### 环境变量配置
在执行本地shell时，会碰到命令找不到的情况，可以在环境变量中配置PATH。位置：【系统管理】->【系统设置】->【全局属性】->
【环境变量】添加PATH，将本地的PATH值填入。保存。本地PATH可以在shell中查看：
```
echo $PATH
```

### git配置
在新建任务时，需要从git服务器拉取源码。如果使用ssh方式访问，那么先添加凭证。

![](assets/jenkins/1-1.png)

类型选择 SSH Username with private key。username填写机器的用户名，private key输入该用户的私钥，**该私钥是和git服务器中
配置的公钥对应的私钥**。

git配置添加git库，并选择刚才添加的凭证：

![](assets/jenkins/1-2.png)

如果正确，那么git界面的红色错误信息会消失。

### 构建和发布
#### tomcat
构建过程包括两部分：1，mvn编译；2，关闭tomcat，更新程序包，启动tomcat。如下图：

![](assets/jenkins/1-3.png)

代码如下示例如下：
```
clean package -pl mechanical-api -am -Dmaven.test.skip=true  -P dev
```
-pl，构建当前目录下的子模块，-am：表示同时处理选定模块所依赖的模块。
```
#!/bin/bash
# 用于阻止jenkins 关闭脚本衍生的子进程，让如tomcat等程序后台运行。
export BUILD_ID=tomcat_mechanical_admin_build_id
# 1.关闭tomcat
pidlist=`ps -ef|grep apache-tomcat|grep -v "grep"|awk '{print $2}'`
function stop(){
if [ "$pidlist" == "" ]
  then
    echo "----tomcat 已经关闭----"
 else
    echo "tomcat进程号 :$pidlist"
    sudo kill -9 $pidlist
    echo "KILL $pidlist:tomcat关闭成功！"
fi
}

stop

# 复制jar包，有则覆盖
mv -f /home/web/mechanical-artifacts/mechanical-admin.war  /home/apache-tomcat-8.5.35/webapps/
sleep 3s

# 5.启动tomcat
cd /home/apache-tomcat-8.5.35/bin/
./startup.sh

sleep 3s

pidlist3=`ps -ef|grep apache-tomcat|grep -v "grep"|awk '{print $2}'`
if [ "$pidlist3" == "" ]
    then 
       echo "----tomcat启动失败----"
else
    echo "----tomcat启动成功----"
fi
```
#### maven jar包发布
maven顶层构建
```
clean package -Dmaven.test.skip=true
```
执行的shell
```
#!/bin/bash

# 用于阻止jenkins 关闭脚本衍生的子进程，让如tomcat等程序后台运行。
export BUILD_ID=hesicare_dev_build_id
export JAR_PATH=/home/web/services/hesicare-dev


if [ ! -d "$JAR_PATH" ]; then
  mkdir -p "$JAR_PATH"
fi

# 1.关闭运行的程序
stop(){
  pidlist=`ps -ef|grep hesicare-admin-dev|grep -v "grep"|awk '{print $2}'`
  
  if [ ! -z "$pidlist" ] ; then
      kill $pidlist
      echo  "关闭 hesicare-admin-dev ..."
  fi
  
  pidlist1=`ps -ef|grep hesicare-business-dev|grep -v "grep"|awk '{print $2}'`
  
  if [ ! -z "$pidlist1" ] ; then
      kill $pidlist1
      echo  "关闭 hesicare-business-dev ..."
  fi
}

stop

mv -f hesicare-admin/target/hesicare-admin.jar $JAR_PATH/hesicare-admin-dev.jar
mv -f hesicare-business/target/hesicare-business.jar $JAR_PATH/hesicare-business-dev.jar

cd $JAR_PATH
chmod u+x hesicare-admin-dev.jar
chmod u+x hesicare-business-dev.jar

# 等待程序彻底关闭，不然会报出quartz错误。
sleep 2s
nohup java -jar hesicare-admin-dev.jar > hesicare-admin-dev.log &
nohup java -jar hesicare-business-dev.jar > hesicare-business-dev.log &
sleep 5s

pidlist3=`ps -ef|grep hesicare-admin-dev|grep -v "grep"|awk '{print $2}'`

if [ -z "$pidlist3" ]
    then 
       echo "----hesicare-admin-dev启动失败----"
       stop
       exit -2
else
    echo "----hesicare-admin-dev启动成功----"
fi

pidlist4=`ps -ef|grep hesicare-business-dev|grep -v "grep"|awk '{print $2}'`

if [ -z "$pidlist4" ]
    then 
       echo "----hesicare-business-dev启动失败----"
       stop
       exit -2
else
    echo "----hesicare-business-dev启动成功----"
fi
```
#### 远程tomcat
远程部署需要配置Publish over SSH，在增加构建步骤中选择Send files or excute commands over SSH。Publish over SSH，是将需要
发布的文件先传到远程服务器上，然后再执行远程脚本，下图中红色部分：Source files是需要传输的文件，根目录为jenkins任务的
workspace，remove prefix，是移除source files的前缀。remote directory如果没有，那么会创建一个目录。Exec command 用于设置
需要在远程机器上执行的脚本。

![](assets/jenkins/1-4.png)

### gitlab自动触发部署
#### 插件安装
在jenkins上安装插件 Build Authorization Token Root Plugin。该插件可以让匿名用户通过REST请求通过token触发jenkins的job构建。

使用例子如下，下面这种方式可以穿过jenkins权限限制：
```
buildByToken/build?job=jobName&token=token
```
带参数的请求
```
buildByToken/buildWithParameters?job=jobName&token=token&Type=Mexican
```
#### Publish Over SSH 配置
在要部署的服务器上，SSH用户目录下的.ssh文件夹中生产一对密钥，用于ssh 访问和生产插件token。
```
ssh-keygen -t rsa
```
将公钥名称改为authorized_keys，因为Publish Over SSH默认会查找名称为authorized_kyes的公钥文件。
```
mv id_rsa.pub authorized_kyes
```
将私钥配置到Publish Over SSH 中

![](assets/jenkins/1-5.png)

配置完成后点击下面的测试连接是否可以连接。

#### 触发构建配置
随机一个token，并将它配置到jenkins job中的构建触发器->触发远程构建中，如下：
```
openssl rand -hex 12
```
![](assets/jenkins/1-6.png)

#### gitlab配置集成
在gitlab中找到需要构建的项目，找到setting->Integrations，在URL中填入如下内容：
```
http://jenkins.example.com/buildByToken/build?job=jobName&token=token
```
将jenkins.example.com修改为你自己jenkins的主路径，jobName修改为需要触发的job名称，token修改为jenkins前面配置的token。

勾选接下来对应的配置，然后点击 Add webhook按钮，如下图。

![](assets/jenkins/1-7.png)

在生产Webhooks后，点击test-Push events看是否能够触发jenkins构建成功。

![](assets/jenkins/1-8.png)

如果出现下面错误 不允许向本地网络发出请求,这是因为在本地网络配置的，管理员->Admin Area->Settings->Network->
Outbound requests,勾选并保存下图中的红色选框。

![](assets/jenkins/1-9.png)

