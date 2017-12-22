---
title: Git Push免密码设置
date: 2017-12-22 08:02:48
tags: git
---

## **使用.git-credentials保存验证**

```sh
$ cd
$ touch .git-credentials
$ emacs .git-credentials
https://{timting***}:{********}@github.com
```

```sh
$ cd ~/.emacs.d/
$ git push -u origin master
Username for 'https://github.com':   C-c C-c
```

**去掉{}**

```sh
$ emacs ~/.git-credentials
https://timting***:********@github.com
$ git config --global credential.helper store

$ cd
$ cat ~/.gitconfig
[user]
	email = timtingwei@hotmail.com
	name = timtingwei
[credential]
	helper = store
```

现在push不再需要验证。

**但我不希望用文件保存我的密码**

```sh
$ ls -ld .git-credentials
-rw------- 1 tim tim 42 12月 22 15:00 .git-credentials
tim@htwt:~$ sudo chmod 200 .git-credentials
[sudo] password for tim: 
tim@htwt:~$ ls -ld .git-credentials
--w------- 1 tim tim 42 12月 22 15:00 .git-credentials
tim@htwt:~$ git config --global credential.helper store
```

重启bash

```sh
tim@htwt:~/.emacs.d$ git push origin master
fatal: unable to open /home/tim/.git-credentials: Permission denied
```

=> 修改权限的方法没用, 别人看不到, git也看不到。

======

## **安全性存疑，因此改用设置cache**
```sh
$ rm ~/.git-credentials
```
通过设置git的cache的话可以让它记住密码之后自己设置一个cache有效时间 这样也一定程度保证了一些安全性。

```sh
$ git config --global credential.helper cache
\# Set git to use the credential memory cache
```

```sh
$ git config --global credential.helper 'cache --timeout=3600'
\# Set the cache to timeout after 1 hour (setting is in seconds)
```
======

refwebsite:
[1], https://www.zhihu.com/question/31836445
[2], http://www.jianshu.com/p/f54053afecf2
[3], https://help.github.com/articles/caching-your-github-password-in-git/
