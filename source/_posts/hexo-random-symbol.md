---
title: hexo博客中文乱码
date: 2017-11-23 09:52:42
tags: 
- hexo
- UTF-8
---

## issue:

```
$ hexo g && hexo s
```

此时[localhost](http://localhost:4000/)中出现如下中文乱码, 英文部分正常。
> ��Ȼ���(depth buffer)

此时\_config.yml中:

```
language: zh-Hans
```
## hack:

> 1. Notepad++打开出现中文的\*.md, 此时右下角为ANSI格式.
> 2. 格式->转为UFT-8编码格式
> 3. 保存
> 4. $ hexo clean && hexo g && hexo s

此时[localhost](http://localhost:4000/)中
> 深度缓存(depth buffer)

## issue++:

> * 批量转化post中的md文件
> * 自动生成UTF-8格式的md文件

------
reference:
[1]: 



