---
title: golang 环境和工具
published: true
category: golang
---

## <span id="1">golang安装</span> 
### 源码安装

###标准包安装
地址：https://golang.org/dl/

## <span id="2">开发环境安装</span> 

### LiteIDE
LiteIDE是国人开发的golang开发工具。

首先保证go语言安装包已正确安装，GOROOT和Path在系统环境变量中已经正确配置。GOROOT是go的安装地址，GOPATH是go的工作地址，如
```
GOROOT = C:\go       GOPATH= E:\gowork
```

LiteIDE的[下载地址](http://sourceforge.net/projects/liteide/?source=typ_redirect)。下载后一路next安装。

### sublime

sublime中在安装了gosublime插件后，可以通过`ctrl+shift+左键`实现代码跳转。

### windows平台下gcc安装
在Go的源代码中，有些部分是用Plan 9 C和AT&T汇编写的，因此假如你要想从源码安装，或者在开发的时候调试程序，就必须安装C的编译工具。在Windows系统中，你需要[下载](https://sourceforge.net/projects/mingw-w64/)安装MinGW，然后通过MinGW安装gcc，并设置相应的环境变量。

#### 安装时的参数说明
 1. architecture x86-64(64位系统选择)
 2. Threads:posix(shouldn't actually matter)
 3. Exception:seh(shouldn't actually matter)
 4. Build revision:Lastest available(0 at time of writing)

安装完成后将安装程序的bin目录添加到path环境变量中，如：

```
C:\Program Files\mingw-w64\x86_64-6.2.0-posix-seh-rt_v5-rev1\mingw64\bin
```  
  
#### Minimalist GNU for Windows  
如果上面的mingw地址下载安装不成功，可以通过另一个[下载](https://sourceforge.net/projects/mingw/)地址安装。  

 1. 下载后一路默认next，注意要安装gui界面（默认选中）。
 2. 运行mingw安装管理器，选中mingw32-base（c编译器）其他按需选择。
 3. 点击菜单/Installation/Apply Changes,等待安装完成。 
 4. 配置环境变量，默认安装路径为`C:\MinGW\bin`，添加到环境变量Path下。
 
#### 使用go get命令安装delve调试器
```
go get -v -u github.com/peterh/liner github.com/derekparker/delve/cmd/dlv
```


## <span id="3">go tool</span> 
### go get
获得远程包：
```
go get github.com/astaxie/beedb
```
go get 会自动获取所依赖的第三方包，-u参数可以自动更新包。

### go fmt
强制格式化代码，一般的go编辑器都自带自动格式化功能，-w参数，格式化结果写入文件。
```
gofmt –w file
```

### go doc
查看程序文档，如下，查看encoding/json包的文档。
```
go doc encoding/json 
```
查看包下面的函数文档
```
go doc encoding/json Marshal
```
查看源代码
```
godoc –src encoding/json Marshal
```

发布本地go文档
```
godoc –http=:8088
```
访问本地的localhost:8088会访问到golang.org的copy版。

### go vet
go vet会检查程序中的错误。


