---
title: Markdown语法整理(不定期更新)
date: 2017-11-21 13:31:03
tags: "markdown"
categories: 

---


In this post, I will focus on use of Markdown and hexo push.
 
> 1. title level
> 2. insert a link
> 3. highlight the code
> 4. three lists of point
> 5. insert a note"[^code]"
> 6. bold fonts
> 7. reference
> 8. edit a formula
> 9. draw a flow picture
> * [补充](#补充)

reference website: [Cmd Markdown reader](https://www.zybuluo.com/mdeditor#fn:latex)



## 1. Title level

### level 3-0
#### level 4-0
### level 3-1
#### level 4-1
#### level 4-2

## 2. Insert a link.

### [Tim's Blog](https://timtingwei.github.io/)
### [Tim's Blog][2]

```
### [Tim's Blog](https://timtingwei.github.io/)
### [Tim's Blog][2]
```

## 3. Highlight the code
```python
@requires_authorization
class SomeClass:
    pass

if __name__ == '__main__':
    # A comment
    print 'hello world'
```


```cpp
#include <iostream>

class Foo {
 public:
  int f();
};

int main() {
  // ...
  return 0;
}
```



## 4. three lists of point

### Bulleted List
> * first line
> * second line
> * third line

### Numbered List
> 1. first line
> 2. second line
> 3. third line

### TODO list
- [ ] first todo 
- [ ] second todo
- [x] third todo -> Done

## 5. insert a note[^code]
LaTex formula support [LaTex][^LaTex]
high light code [code][^code]
insert a web note [MathJax][1]

## 6. bold fonts

** bold fonts ** by adding two asteriskes *  as prefix and suffix respectively


## 7. Reference 

> You can insert a reference by ">",
and input enter to create a new line,
This is the end line. Until the next symbol occur.

## 8. edit a formula
$$E=mc^2$$    use $$

## 9.draw a flow picture

```flow
st=>start: Start
op=>operation: Your Opreation
cond=>condition: Yes or No?
e=>end

st->op->cond
cond(yes)->e
cond(no)->op
```

[^LaTex]: support **LaTex** edit and display, such as: $\sum_{i=1}^n a_i=0$, visit [MathJax][1] for more information.
[^code]: include Java, Python, JavaScript etc.
[1]: http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
[2]: https://timtingwei.github.io/

------

# 补充

> * [背景色灰](#背景色灰)
> * [实现页面内跳转](#实现页面内跳转)
> * [删除线](#删除线)
> * [插入图片](#插入图片)
> * [标记文本颜色](#标记文本颜色)
20171224之后补充但未作整理。本来可以将每个语法分成多个博客离散列出，但后来出于对博客页面和知识索引的考虑，还是放在一个post内，也许将来会又变动，但是先不提前优化。


## 背景色灰
\`gray background\`

`gray background`

背景灰可以代替 反斜杠 \\,去完成注释。这个很好用。原理估计和代码块差不多吧。

## 实现页面内跳转

`[create an anchor](#anchors-in-markdown)`

[create an anchor](#anchors-in-markdown)

\===========
refwebsite:

[Anchors in Markdown](https://gist.github.com/asabaylus/3071099)


### anchors in markdown
	skip into this title


## 删除线

`~~删除一句~~`
~~删除一句~~

`~~删除`
`多行~~`
~~删除
多行~~

`~~删除包含语法[mituh’s notes][timtingwei.github.io]~~`
~~删除包含语法\[mituh’s notes\]\[timtingwei.github.io\]~~

====
不知道还有其他方法否？

\==========
refwebsite:

https://en.wikipedia.org/wiki/Markdown



## 插入图片

`![Logo](/images/logo.png)`
![Logo](/images/logo.png)

\===========
refwebsite:

hexo图片的其他用法：https://hexo.io/zh-cn/docs/asset-folders.html
https://webapps.stackexchange.com/questions/29602/markdown-to-insert-and-display-an-image-on-github-repo

## 标记文本颜色

`<span style="color:blue">*This is Blue italic.*</span>`
<span style="color:blue">*This is Blue italic.*</span>

`<span style="color:red">**This is Red Bold.**</span>`
<span style="color:red">**This is Red Bold.**</span>

相比之下,
italic 给人轻松的感觉，
blod有种警告意味。

\===========
refwebsite:

https://stackoverflow.com/questions/35465557/how-to-apply-color-in-markdown

## 换行
段落前后是空行
段落内插入`<br>`换行

test1
test2

```
test1
test2
```


test1

test2

```

test1

test2

```

test1<br>
test2<br>
```
test1<br>
test2<br>
```

\====
ref:
http://xianbai.me/learn-md/article/syntax/paragraphs-and-line-breaks.html

## next one ?

---------------------------------------------------------------
以上是我的个人笔记收录，markdown主要用来写笔记和写博客
其他有什么好玩的用法，欢迎指教。
timtingwei@gmail.com
