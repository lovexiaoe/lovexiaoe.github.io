---
title: itellij idea
published: true
category: java
---

## 技巧和设置 ##
### 运行单个main方法 ###
1. 修改Runedit configurations，将build改为build no error check，如果要项目默认的构建不检查错误，
点击菜单Run->Edit Configurations->左边Templates->application,将默认的build改为build,no error check。
2. 右键要运行的main类，点击Recompile “XXX.java”
3. 点击右键运行即可。

### 取消双击shift自动打开Search Everywhere ###
1. 双击shift打开Search Everywhere,找到Action选项， 查找registry 注冊表，找到key为ide.suppress.double.click.handler的value打勾。
2. 在设置—》快捷键中重新设置Search Everywhere的快捷键。如alt+s。

## 常用快捷键 ##

### 取消Win10 ctrl+space 被切换输入法占用。
1. win+Q打开搜索，找到语言。
1. 找到键盘—》打开输入语言热键。
1. 在热键中找到ctrl+space对应的选项，修改为左Alt+Shift+BSLASH，快捷键为你平时用不到的组合。
1. 重启后，生效。

### 窗口 ###
 1. Shift+左键 关闭窗口

### 编辑 ###
 1. Ctrl+Shift+Enter：在if、for、函数等等后补上{}或者;使代码语句完整。
 1. Shift+Enter：在当前行的下方开始新行。
 1. Ctrl+Alt+Enter：在当前行上方插入新行。
 1. Ctrl+Z：撤销
 1. Ctrl+Shift+Z：重做
 1. Ctrl+Shift+J：向下合并行。
 1. Ctrl+D：在下面复制一行。
 1. Ctrl+Y：删除一行。
 1. Ctrl+Shift+向上的箭头：上移一行。
 1. Ctrl+Shift+向下的箭头：下移一行。
 1. Ctrl+Shift+V：选择剪切板的内容。
 1. Ctrl+Shift+C: 复制文件的文件路径。
 1. Ctrl+Shift+Alt+C: 复制文件的包路径。
 
### 光标操作
1. Ctrl+Shift+M：移动至大括号。
1. Ctrl+]：诸如{}围起来的代码块，使用该快捷键可以快速跳转至代码块的结尾处。
1. Ctrl+[：快速跳至代码块的开始处。
1. Ctrl+右箭头：移动到下一个word
1. Ctrl+左箭头：移动到上一个word
1. Ctrl+Shift+右箭头：选择到下一个word
1. Ctrl+Shift+左箭头：选择到上一个word
1. Ctrl+Shift+左箭头：选择到上一个word
1. Alt+Down：下一个方法。
1. Alt+Up：上一个方法。
1. Ctrl+PageUp：移动至页面顶部。
1. Ctrl+PageDown：移动至页面底部。
1. Ctrl+Home：移动至文件开头。
1. Ctrl+End：移动至文件结尾。
1. Home：移动至行首。
1. End：移动至行尾。
1. Ctrl+W：选中整个单词。
1. Ctrl+Shift+W：取消选中整个单词。

### 多光标和范围选择
1. Alt+Shift+Click：添加/删除光标。
1. Alt+Shift+Insert：切换列选择模式。
1. 双击Ctrl+Up：（双击后不松开Ctrl）向上插入光标。
1. 双击Ctrl+Down：（双击后不松开Ctrl）向下插入光标。
1. Alt+J：选择单位下次出现的位置。
1. Alt+Shift+J：取消选择单位出现的位置。
1. Ctrl+Alt+Shift+J：选择单位所有出现的位置。
1. Alt+Shift+鼠标中键：创建矩形选择。
1. Alt+鼠标左键：拖拽以创建矩形选择。
1. Ctrl+Alt+Shift+鼠标左键：拖拽以创建多个矩形选择。

### 查找 ###
 1. Ctrl+F：当前文件中查找。
 2. Ctrl+R：替换字符串。
 3. Ctrl+Shift+F：全局文件中查找。
 4. Ctrl+Shift+R：全局文件中替换。
 5. Alt+F7：查找当前变量的使用，并显示列表。
 7. Alt+F3，高亮显示所有该文本，按 Esc 高亮消失，然后按 F3 向下选择，Shift+F3向上选择。
 8. Ctrl+F7：可以查询当前元素在当前文件中的引用，然后按 F3 向下选择，Shift+F3向上选择。
 1. F2：高亮错误或者警告。

 
### 导航 ###
 1. Ctrl+N(和双击shift作用相同)：查找 类文件。
 2. Ctrl+Shift+N：查找文件。
 3. Ctrl+U：找到重载或实现方法的父级定义。
 4. Ctrl+F12：显示文件的文件结构。
 5. Alt+数字：显示或关闭侧边栏。
 6. Ctrl+H：显示当前类的继承层次。
 7. Ctrl+Shift+H：显示当前方法的继承层级。
 8. Ctrl+Alt+H：显示当前方法的调用层级。
 9. F2：在一个文件中定位至下一个错误处。
 10. Shift+F2：在一个文件中定位至前一个错误处。
 11. Ctrl+E 打开最近的文件。
 1. Ctrl+G 跳转至指定行。
 1. Alt+F1：Select In，在其他窗口查找并定位。
 1. Ctrl+Alt+Left：上一个编辑位置。
 1. Ctrl+Alt+Right：下一个编辑位置。
 
### 辅助编码
1. Alt+Enter: 显示建议操作。
1. Ctrl+Space: 代码补全。
1. Ctrl+Shift+Space: 类型匹配代码补全。
1. Ctrl+P: 参数信息提示。
1. Ctrl+Q： 快速文档。

### 代码生成 ###
1. Ctrl+O：重写方法。
2. Ctrl+I：生成一个接口中的方法。
3. Alt+Insert：产生构造方法、getter/setter等方法。
4. Ctrl+Alt+T：将选中的代码使用if、while、try/catch等包装。
2. Ctrl+Shift+Alt+T：弹出重构菜单。
5. Ctrl+Shift+Delete：去除相关的包装代码。
6. Ctrl+J：插入代码模板。
7. Ctrl+/：使用//进行注释
8. Ctrl+Shift+/：使用/**/进行注释
9. Ctrl+Alt+L：格式化代码
10. Ctrl+Alt+I：自动缩进行
11. Ctrl+Alt+O：优化import
12. Ctrl+Alt+V：根据表达式创建变量。
13. Ctrl+Alt+F：根据表达式添加该类的字段。

### bookmark ###
1. F11: 书签(标记/取消)。
1. Ctrl+F11: 符号型书签(标记/取消)。
1. shift+F11: 查看书签列表。
1. ctrl+shift+1 快速标记/取消书签1
1. Ctrl+数字：切换到符号型书签

### 其他 ###
1. Ctrl+Tab: 打开切换器，显示所有工具窗口和编辑窗口。
1. 在设置Keymap-Tool Windows中设置工具窗口快捷键，根据自己的习惯设置Project和Structure快捷键。
1. Esc：聚焦编辑窗口。

### 版本控制
1. Ctrl+T：拉取代码。
1. Ctrl+Shift+K：推送代码。
1. Ctrl+K：提交代码。
1. Alt+~：打开版本控制操作面板。

### javadoc注释快捷键
在设置Keymap-other-fix doc comment中设置快捷键为Alt+D

## 插件安装 ##
### 社区版安装spring插件 ###
在file'settings'Plungins中搜索spring找到spring Assistant。选择下载，然后就可以在new project的时候找到合适的模板了。

### Lombok安装 ###
1. File-->settings-->Plungins搜索Lombok插件并安装，重启。
2. 在pom文件中添加依赖。

    ```XML
    <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.2</version>
    </dependency>
    ```

### 安装 Free Mybatis ###
1. File-->settings-->Plungins搜索free Mybatis插件并安装，重启。
如果插件中心加载不出来，那么在`https://plugins.jetbrains.com/` 搜索 free mybatis下载到本地进行安装。

### 其他插件的安装 ###
1. markdown Navigator/markdown 支持markdown编辑和预览
2. string manipulation 支持文本的强大的编辑能力。
3. .ignore 忽略文件支持。
4. findBugs 找出bug。
5. checkstyle 代码风格检验，一般不用。

## 报错及解决方法 ##
### Usage of API documented as \@since 1.6\+错误 ###
#### 出错原因 ####
有些代码会出现类似的错误，是因为IDE的编译级别低于代码的编译级别。如果是使用maven编译，那么maven对模块的默认编译级别为5。
#### 解决方法 ####
将IDE的默认编译级别调整为高于报错的编译级别。如果是maven，那么需要在pom中添加插件配置,将编译级别调整为高于报错的编译级别。
```XML
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.6.0</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>

```
## 编码和换行符设置 ##
### 编码设置 ###
打开设置ctrl+alt+s，Editor->File Encoding，将所有编码改为UTF-8，BOM选项改为with No BOM。
### 换行符设置 ###
打开设置ctrl+alt+s，Editor->Code Style，Schema Default->general->Line Separator 改为Unix 格式。Schema Project->general->Line Separator 改为Unix 格式。

## 模板 ##
### 方法注释模板 ###
ctrl-alt-s打开设置，Editor-Live Templates 添加模板组comment和模板cfm。
模板的各参数如下：
- Abbreviation: *c
- Description comment for method
- Template Text 
    模板不能以/*开头，不然取不到方法参数和返回值。$END$ 内建变量，用于定义鼠标，使用tab切换需要
    编辑的变量。
    ```
    **
     * $END$
     * @author: zhaoyu
     $params$
     * @Return $return$
     */
    ```

- Template Text下面的Application in... 点击change 选择java-Comment。
- Edit variables 选择变量对应的函数表达式，如果希望变量存在后不再编辑，选中Skip if defined。

    - params
    
        ```groovy
          groovyScript("def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList(); for(i = 0; i < params.size(); i++) {result+='* @param ' + params[i] + ((i < params.size() - 1) ? '\\r\\n' : '')}; return result", methodParameters())
        ```
    - return:methodReturnTypes()。
    
Options选择Reformat according to style。保存。
然后输入`/*c` 按tab键，会自动生产方法的注释。

### Header模板 ###
ctrl-alt-s打开设置，Editor-File and Code Templates-Include-File Header。

- 添加内容如下

    ```java
     /**
     * @Description: 
     * @Author: zhaoyu
     * @Date: ${DATE}
     */
    ```
  
- 勾选Enable Live Templates

## devtools 热部署配置 
1. 在pom.xml中添加依赖

    ```
    dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-devtools</artifactId>
       <optional>true</optional>
    </dependency>
    ```
2. 在yml中配置如下,默认为true。

    ```yaml
    spring:
      devtools:
        restart:
          enabled: true #是否支持热部署
    ```

3. idea配置

    - ctrl+alt+s 打开配置，在compiler中勾选build project automatically。
    - 快捷键：ctrl + shift + alt + / 选择Registry, 勾选 Compiler autoMake allow when app running

4. 重启项目，完成配置。

## 自动保存的密码修改
打开settings，搜索passwords可以看到idea默认将所有的密码保存在keepass中，选择do not save选项。重启idea，
然后保存的密码会被清除，在中心输入密码后，将passwords的设置回到keepass。则密码修改成功，如下图。

![](/assets/java/idea-password-reset.png)

## 自动导入
打开设置（ctrl+alt+s），搜索‘auto import’ 勾选Add unambiguous imports on the fly 和 optimize imports on the fly 两项。

## 修改idea项目方法
1. 更改project的Project name和Project compiler output。
1. 更改Modules的Name
1. 删除Artifacts下的两个打包配置（稍后会再自动生成）
2. 更改pom.xml的artifactId
3. 退出idea，找到项目路径，更改项目文件名
1. 然后idea再打开项目