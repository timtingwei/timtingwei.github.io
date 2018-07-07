---
title: Git Request 提交, 合并
date: 2018-07-06 14:35:29
tags: [git]
---

最近和学长[HankDaly](https://github.com/HankDaly)共同contribute一个项目。
涉及到三个操作:
1, HankDaly从我的github上获取代码
2, HankDaly修改代码并提交给我
3, 我对代码进行审核合并到项目中


## 1, HankDaly从我的github上获取代码(从项目维护者那获取代码)

a, 在github上fork项目, timtingwei/cppd  => HankDaly/cppd
b, HankDaly, clone自己的项目到本地
```sh
git clone https://github.com/HankDaly/cppd.git
```

## 2, HankDaly修改代码并提交给我

a, 完成修改后, git push origin master 上传本地当前分支代码到远程master分支
b, 打开github, 在自己远程仓库的项目中, 找到pull request, 新建一个pull request
c, 选择自己要request的分支, 和base分支, 当前默认都为master
![我拿自己fork HankDaly/test 做的实验](/images/pull-request-new-requset.png)
d, 创建这次pull request

## 3, 我对代码进行审核合并到项目中
在没有冲突的情况下可以直接在github上合并。
在冲突情况下必须手动pull下来, diff后修改矛盾, 再merge, pull request会自动消失。

这里主要做的事情是, 手动在另外一个branch中, 审阅request上来的代码, 再与master合并

这里我前面在pull的时候, 总没有git checkout -b hank-master

我所推测的步骤应该是:

a, 切换到一个专门用于审阅request的分支
```sh
git checkout -b hank-master
```

b, 在当前分支下, 拉下来学长的push
```sh
git pull https://github.com/HankDaly/cppd.git
```
没有conflict的话可以直接pull下来。

如果这里有confilict
```
CONFLICT
```
```
git diff
```
手动将confilict改完, 就是保存一份没有冲突的

c, 提交对代码的改动
```sh
git add .
git commit -m "merge hank's code"
```

d, 切换到master分支合并代码
```sh
git checkout master
git merge hank-master
```

e, 提交到远程
```
git push origin master
```



什么时候会出现conflict?
我改动的同时, request也改动了相同的代码。

