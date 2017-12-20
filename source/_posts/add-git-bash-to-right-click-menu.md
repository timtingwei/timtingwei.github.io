---
title: 添加git bash到右键菜单 
date: 2017-11-23 11:52:34
tags:
- git
---

## issue:

因为.git文件所在的目录如下：
> C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\bin\d3d

当在bash中cd到该目录时：
```
$ cd C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\bin\d3d
bash: syntax error near unexpected token `('
```
试图通过右键进入bash

## hack:

> 1. CMD中输入"regedit"
> 2. 切换到 HKEY_CURRENT_USER\SOFTWARE\Classes\Directory\Background\shell
> 3. 右键点击 "shell" 选择 New > Key. 将KEY命名为 "Bash"
> 4. 设置值为 "open in Bash" 
> 5. 创建一个新的KEY命名为"command". 设置值为git-bash.exe 路径.

此时在文件夹中右键, 出现Open in bash选项.

以上为通过注册表添加bash到右键的方法，但还是建议重装git

------

reference:
[1]: https://www.zhihu.com/question/48091139 @轩辕Rowboat的回答
