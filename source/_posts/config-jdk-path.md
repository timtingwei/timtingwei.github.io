---
title: 配置jdk环境和classpath
date: 2018-08-06 15:33:22
tags: Java, 配置, Mac
---

学《Algorithms》这本书的时候, 教材有一套自己的标准库
```
Getting started. To use this class, you must have StdIn.class in your Java classpath.
If you used our autoinstaller, you should be all set. 
Otherwise, either download stdlib.jar and add to your Java classpath or download StdIn.java and put a copy in your working directory.
```
ref:https://introcs.cs.princeton.edu/java/stdlib/javadoc/StdIn.html


```sh
ls -l /usr/bin/java
lrwxr-xr-x  1 root  wheel  74 Jun 17 16:18 /usr/bin/java -> /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java
```

这个只是替身路径, Go to这个路径, 展开后在
`/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home`找到真实路径


编辑~/.bash_profile中(我的是这个)
```sh
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home #jdk安装路径   
export PATH=$JAVA_HOME/bin:$PATH 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/stdlib.jar
```

需要把从《Algorithms》上下载下来的stdlib.jar放入JAVA_HOME的lib文件中.

保存并
```sh
source .bash_profile
```

