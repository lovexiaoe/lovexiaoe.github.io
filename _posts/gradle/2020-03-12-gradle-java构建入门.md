---
title: gradle-java构建入门
published: true
category: gradle
---

### java插件
java 插件为工程定义了许多默认值，如Java源文件位置。如果你遵循这些默认规则，那么你无需在你的脚本文件中书写太多代码。当然，
Gradle 也允许你自定义项目中的一些规则。
使用java插件在脚本中添加如下代码：
```groovy
apply plugin: 'java'
```
#### 构建
* 构建项目
```
gradle build
```
* clean
删除 build 目录以及所有构建完成的文件。
* assemble
编译并打包 jar 文件，但不会执行单元测试。一些其他插件可能会增强这个任务的功能。例如，如果采用了 War 插件，这个任务便会为你的
项目打出 War 包。
* check
编译并测试代码。一些其他插件也可能会增强这个任务的功能。例如，如果采用了 Code-quality 插件，这个任务会额外执行 Checkstyle。

#### 外部依赖

* 定义maven仓库
```groovy
repositories {
    mavenCentral()
    jcenter()
    
    maven {
        url 'https://repo.spring.io/libs-milestone'
    }

    maven {
        url "${project.rootDir}/build/repo"
    }
}
```
* 添加依赖
```groovy
dependencies {
    compile project(":common-swagger")
    compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
```
* 自定义MANIFEST.MF
```groovy
sourceCompatibility = 1.5
version = '1.0'
jar {
    manifest {
        attributes 'Implementation-Title': 'Gradle Quickstart', 'Implementation-Version': version
    }
}
```
* 发布jar包
将jar包发布到某个仓库中：
```groovy
uploadArchives {
    repositories {
       flatDir {
           dirs 'repos'
       }
    }
}
```

#### 多项目配置
定义一个如下结构的多项目结构：
```
parentproject/
  api/
  services/webservice/
  shared/
```
parentproject为多项目机构，下面包括了3个项目api、services/webservice、shared。shared是api和webservice公用的项目。多项目需要
在根项目目录中创建一个配置文件settings.gradle:
```
include "shared", "api", "services:webservice", "services:shared"
```
对多项目构建而言，总有一些共同的配置.在本例中，我们会在根项目上采用配置注入的方式定义一些公共配置。
```groovy
subprojects {
    apply plugin: 'java'
    apply plugin: 'eclipse-wtp'
    
    sourceCompatibility = '1.8'
    targetCompatibility = '1.8'
    
    repositories {
       mavenCentral()
    }
}
```
上面我们为每个子项目应用了java插件，我们可以在根项目目录进行编译，测试，打包等所有操作。

