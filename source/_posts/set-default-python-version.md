---
title: 将python默认版本设置成python 3
date: 2018-06-21 00:04:46
tags:[python, pip, alias, shell]
---

最近看知乎[有哪些命令行的软件堪称神器？][https://www.zhihu.com/question/59227720/answer/286665684]问题, 韦易笑的回答中, 觉得他推荐的cppman小工具不错, 是用来查看cpp文档的。但是
> * Note that cppman requires Python 3. Make sure that either pip is configured for Python 3 installation, your default Python interpreter is version 3 or just use pip3 instead.
需要Python3, 以及pip3

因此, 我选择将默认的python, pip设置成3 version

1, emacs里打开shell
2, ~/.bash_profile 中添入
	\# Changing the default python version to 3
	alias python='python3'
	alias pip='pip3'
3, $ source ~/.bash_profile

现在, shell中输入python, 默认是python 3.6.5了

----
ref:
[1]:https://stackoverflow.com/questions/18425379/how-to-set-pythons-default-version-to-3-3-on-os-x
