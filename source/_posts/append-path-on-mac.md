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
