---
title: eclipse自动补全
published: false
category: java
---

## 解决代码的自动提示问题：
- “window”→“Preferences”
- 选择“java”，展开，“Editor”，选择“Content Assist”。
- 选择“Content Assist”，然后看到右边，右边的“Auto-Activation”下面的“Auto Activation triggers for java”这个选项。
其实就是指触发代码提示的就是“.”这个符号。
- “Auto Activation triggers for java”这个选项，在“.”后加abcdefghijklmnopqrstuvwxyz字母，方便后面的查找修改。
然后“apply”，点击“OK”。

## 取消“空格”、“=”自动上屏：
- 先找到相关的插件打开Plug-ins View找到插件org.eclipse.jface.text,右键点击,选择import as Source Project,导入完成后,
在你的workspace就可以看到这个project了。
- 有些eclipse插件导出没有源代码 ，如果你导出的插件没有源代码，则需要下载 Eclipse RCP版本（该版本修改源代码比较方便，
能自动导入源代码），再进行导出就可以看到源代码。
- 修改代码在src/org/eclipse/jface/text/contentassist/CompletionProposalPopup.java文件中,找到这样一行代码
    ```
    char triggers = t.getTriggerCharacter();if(contains(triggers,key))
    ```
- 在那行if判断里面,eclipse会判断key(就是你按下的键)是否在triggers中,如果是,那就触发下面的第一行提示上屏的代码.  所以我们要做的
就是把空格和=号排除就可以了:
    ```
    if(key!='='&&key!=0x20&&contains(triggers,key))
    ```
  
- 把修改好的org.eclipse.jface.text导出， 右键点击你的workspace里的org.eclipse.jface.text,  选择export-->Deployable plugins 
and fragments, next,destination 选择archive file,然后finish.  你就可以在zip文件里看到生成好的jar ,用它替换掉eclipse/plugins
里面的同名jar包,就可以了。

## 自动管理格式化和自动管理import

除了手动执行这两个功能外，你还可以让Eclipse在保存文件的时候自动格式化源代码并自动管理import语句。要做到这个，
在Eclipse中，到 Window -> Preferences -> Java -> Editor -> Save Actions并启用 Perform the selected actions on save，
选中 Format source code和 organize imports。 
