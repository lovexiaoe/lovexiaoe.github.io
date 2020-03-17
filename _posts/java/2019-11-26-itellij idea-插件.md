---
title: itellij idea-插件
published: true
category: java
---

## maven-tomcat插件
maven-tomcat插件，可以直接部署程序到tomcat。     
为了提高开发的效率，我们需要在IDE中开发后发布到tomcat，进行调试，特别在本地开发环境中，应用广泛。

* 首先我们需要获取tomcat的远程执行脚本的权限。     
在本地Tomcat中配置用户和权限：编辑Tomcat安装目录下的conf/tomcat-user.xml， 加入如下内容：
```
<role rolename="manager-gui" />
<role rolename="manager-script" />
<user username="tomcat" password="tomcat" roles="manager-gui, manager-script"/>
```
如要需要部署到远程的tomcat，还需要修改其他一些配置，因为Tomcat默认只有本地才能拥有管理权限，这里不再深入说明。
* 配置完权限后，重启tomcat。      
* maven配置       
在需要部署的项目下修改pom.xml，添加tomcat-maven插件。
```
<plugin>
     <groupId>org.apache.tomcat.maven</groupId>
     <artifactId>tomcat7-maven-plugin</artifactId>
     <configuration>
         <port>8080</port>
         <!-- 部署的路径 -->
         <path>/project-path</path>
         <!-- tomcat的地址和端口，manager/text是固定的 -->
         <url>http://127.0.0.1:8080/manager/text</url>
         <username>tomcat</username>
         <password>tomcat</password>
      </configuration>
</plugin>	
```
* 添加运行配置：       
打开运行配置，点击左上角➕，添加maven运行配置，working directory选择需要运行的项目路径。Command line中填写：
```
clean tomcat7:redeploy
```
最下面Before launch选择构建项目Build。完成后点击OK。
一切正确的话，运行刚才的运行配置，项目就可以部署到tomcat了。

* 调试        
如果需要对程序调试，将上面的配置复制一份，Command line中的命令修改为：
```
tomcat7:run
```
然后关闭已启动的tomcat，点击debug。

