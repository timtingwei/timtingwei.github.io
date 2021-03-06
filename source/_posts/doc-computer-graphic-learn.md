---
title: 关于文档学习方法和图形学学习的随想
date: 2017-12-28 20:59:50
tags: [图形学, OpenGL, 读书, 随想, 方法论, 引擎]
---

上篇笔记[OpenGL绘制三角形附练习](http://timtingwei.github.io/2017/12/25/hello-triangle-three-execises/)来来回回写了三天，当然，除了学习外，我还去练习了网球，还去参加了一次grasshopper草猛歌会；学习openGL之外，我同时在学习dsa, 看了云风的`<<游戏之旅>>`， 查阅了OpenGL与图形引擎的相关资料, 

## BLOG整理文档学习方法
从云风的书中偶得一段话，

`学习新的技术，翻译一本相关的英语著作可以算是捷径。`
> * 比囫囵吞枣的读一遍英文原文要有效的多。
> * 因为有责任感，必须用心搞清楚每一个句子的意思，以免错误而误导阅读你的译作的人们。
> * 即使对此有所了解，翻译后也能更上一层楼。
> * 英语水平也会提高。

`发现产生了一些共鸣。`
其中之一，是之前学习Grasshooper的一段经历，我学grasshopper已经是0.76版本了, NCF那一批人开始学习也是10左右的事情了，那个时候他们组织学运算器的方式也跟这个很相似，就是翻译Help, 所以有了`<<grasshopper运算器手册>>`一书。不知道是否这种是共通的？还是因为他们中也有程序员，亦或者是因为grasshopper变成了程序爱好者，理解了这种方式？

之二是石老师推荐学生翻译`<<Form Space & Order>>, <<Parten Language>>, <<A New Kind of Science>>`等书来学习数字化技术。

我自己也享受到了这些方法的好处，现在又被云风清晰说出了那种模糊的感觉。便开始尝试在写笔记的时候，结合着文档理解下函数原理，并把它们用心整理好。这也是最近开始写博客来的好处，虽然之前一直用evernote记笔记写日记，但是用一种这是要整理给别人看的心态去做一件事情，效果便也很不同，总之这对于技术学习来说是很好的。

## 图形学学习

另外，这几天在跟着OpenGL的教程学习，开始对OpenGL和图形学有了模糊的认识：
> * 图形学是研究用计算机如果画出一个图像的学科。
> * OpenGL教程会教我如何去用封装代码去渲染一个场景，以及背后没展开的图形学原理。
> * 他的封装性，与我原来想象的能够研究一些与计算几何相关的图形问题算法有较大出入。
> * 但是学习封装性能够让我先去了解一个大概，以及算法的具体应用性，等到发现其中核心算法的学习路径，开始学习也为时不晚，一是对图形学有了些理解，二是明白各自使用方向，三是dsa的课程也是那些算法实现的前提，四是每天都能够编写C++找到乐趣和问题。

下面是摘自 <span style="color:red">**Milo Yip**</span> 叶神在[知乎](https://www.zhihu.com/question/24786878)上的回答，要借鉴这个学习轨迹, 并以此勉励自己：

`如何开始用 C++ 写一个光栅化渲染器？`

**Milo Yip**
计算机图形学、编程、C++ 等 7 个话题的优秀回答者
228 人赞同了该回答
@空明流转 开发过高大上的SALVIA， @Yong He 则提到了Larrabee。我来提供另一些观点。

首先，如果从学习角度出发，不必一开始完全根据现时GPU的架构及概念，来用软件复制一遍。现时的GPU主要是基于三角形光栅化及z-buffer。

如果我们从图形学的历史进程来学习，可以这样做练习：

2D部分：
> 1, 光栅化2D点（就是在二维数组上画点，了解色彩基本原理，并解决影像输出问题）
> 2, 光栅化2D直线（布雷森漢姆直線演算法、吴小林直线算法等）
> 3, 2D直线的剪切算法（见Line clipping）
> 4, 光栅化2D三角形（scan conversion）。避免重复光栅化相邻三角形边界的像素（edge equation）。
> 5, 光栅化简单／复杂多边形
3D部分：
> 1, 把顶点从三维世界空间变换至二维屏幕空间，绘画顶点（如银河星系数据），操控摄像机旋转模型。
> 2, 在剪切空间进行3D直线的剪切算法，把顶点连线（如各种三维正多面体）光栅化成wire frame模型
> 3, 以多边形来定义三维模型。使用画家算法来光栅化那些多边形。
> 改为使用深度缓冲。
> 4, 实现简单的纹理映射，先做屏幕空间的插值，然后实现简单的perspective-correct texture mapping。
> 5, 实现简单的顶点光照，使用顶点颜色插值实现Gouraud shading。
> 6, 通过顶点法线插值，实现Phong shading。
> 7, 实现其他贴图技术，如mipmapping（也可试Summed area table）、bilinear/trilinear filtering、bump mapping、normal mapping、environment mapping等。

