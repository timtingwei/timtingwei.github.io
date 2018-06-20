---
title: Mac添加PATH环境变量
date: 2018-06-20 01:15:47
tags: shell 
---

用常规方法安装好npm之后, 
```sh
$ npm --version
bash: npm: commmand not found
```

以为是没有安装后，换用brewhome[https://brew.sh/index_zh-cn.html](参考 - brewhome官网)进行安装
```sh
$ brew --version
```
依旧 command not found

注意到，warning:
```sh
==> Cleaning up /Library/Caches/Homebrew...
==> Migrating /Library/Caches/Homebrew to /Users/htwt/Library/Caches/Homebrew...
==> Deleting /Library/Caches/Homebrew...
Already up-to-date.
Warning: /usr/local/bin is not in your PATH.
==> Installation successful!
```

```
$ echo $PATH
/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Emacs.app/Contents/MacOS/bin-x86_64-10_9:/Applications/Emacs.app/Contents/MacOS/libexec-x86_64-10_9
```

需要手动添加一个PATH
```sh
$ export PATH=/usr/local/bin:$PATH
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Emacs.app/Contents/MacOS/bin-x86_64-10_9:/Applications/Emacs.app/Contents/MacOS/libexec-x86_64-10_9
```

现在npm, brew都可以直接用了

另外一种方法是修改 ~/.bash_profile文件

```
# export local bin
export PATH=/usr/local/bin:$PATH
```

接着, 
$ source ~/.bash_profile

但接着又出现了新的问题, 每次重启emacs, 重启terminal之后, 都需要 source ~/.bash_profile

解决方法如下:
1, 搞清楚用的是哪个shell,
$ echo $SHELL
/bin/bash

2, zsh对应~/.zshrc, bash对应~/.bashrc, 
添加入source ~/.bash_profile

这样的话， 在每次打开shell时候都能自动加载了

-----
ref:
[1]:[https://www.cnblogs.com/caowei/p/mac-path_2013-08-26.html]
[2]:[https://blog.csdn.net/science_lee/article/details/79214127]
