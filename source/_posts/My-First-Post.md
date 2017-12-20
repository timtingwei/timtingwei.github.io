---
title: My First Post
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




