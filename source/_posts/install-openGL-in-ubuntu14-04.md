---
title: 在ubuntu14.04上建立OpenGL编译环境
date: 2017-12-20 13:58:49
tags: OpenGL
---


学习教程[LearnOpenGL-CN][1]前先配置学习环境，对照后面编译时链接的函数库，

```sh
$ g++ -std=c++11 hello_window.cpp -lGLEW -lGL -lGLU -lglfw3 -lX11 -lXxf86vm -lXrandr -lpthread -lXi -ldl -lXinerama -lXcursor
```

需要：
> * GLEW
> * GLFW/GLFW3
> * libgl1-mesa
> * libglu1-mesa

`ps: 不是完全明白这里链接库的对应关系`

==========

参考:[Howto_Install_OpenGL_Development_Environment][2]

**建立基本编译环境**
首先不可或缺的，就是編譯器與基本的函式庫，如果系統沒有安裝的話，請依照下面的方式安裝：
```sh
sudo apt-get install build-essential
```

**安裝OpenGL Library**
接下來要把我們會用到的 Library 裝上去，首先安裝 OpenGL Library
```sh
sudo apt-get install libgl1-mesa-dev
```

**安裝OpenGL Utilities**
OpenGL Utilities 是一組建構於 OpenGL Library 之上的工具組，提供許多很方便的函式，使 OpenGL 更強大且更容易使用。 接下來我們安裝OpenGL Utilities

> 在Linux下你需要链接libGL.so库文件，这需要添加-lGL到你的链接器设置中。如果找不到这个库你可能需要安装Mesa，NVidia或AMD的开发包，

```sh
sudo apt-get install libglu1-mesa-dev
```
**安裝GLFW/GLFW3**
GLFW是一个专门针对OpenGL的C语言库，它提供了一些渲染物体所需的最低限度的接口。它允许用户创建OpenGL上下文，定义窗口参数以及处理用户输入，这正是我们需要的。

GLFW可以从它官方网站的[下载页][6]上获取。

参考网站[How to build & install GLFW 3 and use it in a Linux project][3]
解压后,

```sh
$ sudo apt-get install cmake

$ cmake -G "Unix Makefiles"

$ sudo apt-get build-dep glfw
Reading package lists... Done
Building dependency tree
Reading state information... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.

$ sudo make
$ sudo make install

Install the project...
-- Install configuration: ""
-- Installing: /usr/local/include/GLFW
-- Installing: /usr/local/include/GLFW/glfw3.h
-- Installing: /usr/local/include/GLFW/glfw3native.h
-- Installing: /usr/local/lib/cmake/glfw3/glfw3Config.cmake
-- Installing: /usr/local/lib/cmake/glfw3/glfw3ConfigVersion.cmake
-- Installing: /usr/local/lib/cmake/glfw3/glfw3Targets.cmake
-- Installing: /usr/local/lib/cmake/glfw3/glfw3Targets-noconfig.cmake
-- Installing: /usr/local/lib/pkgconfig/glfw3.pc
-- Installing: /usr/local/lib/libglfw3.a
```

**从[测试源码网站][4]复制案例代码测试**
该案例中#include <GLFW/glfw3.h>


```sh
$ g++ -std=c++11 -c main.cpp

$ ./main.o
bash: ./main.o: cannot execute binary file: Exec format error  &lt;==执行main.o提示不是二进制可执行文件


$ g++ main.o -o main.exec -lGL -lGLU -lglfw3 -lX11 -lXxf86vm -lXrandr -lpthread -lXi
/usr/bin/ld: //usr/local/lib/libglfw3.a(vulkan.c.o): undefined reference to symbol 'dlclose@@GLIBC_2.2.5'
//lib/x86_64-linux-gnu/libdl.so.2: error adding symbols: DSO missing from command line
collect2: error: ld returned 1 exit status
```

You may also need to add the linker options -ldl -lXinerama -lXcursor to get it to work correctly if you are getting undefined references to **dlclose**

```sh
$ g++ main.o -o main.exec -lGL -lGLU -lglfw3 -lX11 -lXxf86vm -lXrandr -lpthread -lXi -ldl -lXinerama -lXcursor

$ ./main.exec
```


-l<Library>  告訴 gcc 去連結指定 Library
-o<file>     指定執行檔的輸出名字

执行:

$ ./main.exec


~~\**安装glut\*\*~~

~~\`\`\`sh
sudo apt-get install libglut-dev
\`\`\`~~

~~沒有libglut-dev可以改用
\`\`\`sh
sudo apt-get install freeglut3-dev
\`\`\`~~

~~設定編譯參數與編譯
在這之前，我們需要一個測試範例，example.c~~ [如下][2]：

~~有了測試範例、基礎編譯環境與各個 OpenGL 相關的函式庫之後，我們所需要做的就是告訴編譯器我們有安裝 OpenGL 函式庫，編譯程式時要連結這些函式庫。~~

~~gcc example.c -o example.out -lGL -lGLU -lglut
因為我們安裝了 OpenGL Utility Toolkit ，它是建立在 OpenGL Utilities 與 OpenGL Library 之上，因此我們可以簡單連結 OpenGL Utility Toolkit 的函式庫就可以達到我們的目地了。下面的編譯參數跟上面的是同樣效果：~~

~~gcc example.c -o example.out -lglut
下面簡單說明一下所使用的 gcc 參數~~

~~-l<Library>  告訴 gcc 去連結指定 Library
-o<file>     指定執行檔的輸出名字~~

~~執行
接下來我們就可以來看看我們的結果，如果看到右圖，代表成功了!~
./example.out~~


**编译和链接GLEW**

对于用GCC编译的Linux用户建议使用这个命令行选项-lGLEW -lglfw3 -lGL -lX11 -lpthread -lXrandr -lXi。没有正确链接相应的库会产生 undefined reference(未定义的引用) 这个错误。

```sh
$ sudo apt-get build-dep glew
Reading package lists... Done
Building dependency tree       
Reading state information... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.

$ sudo make
$ sudo make install
```

==========

**测试教程中的案例代码**

复制 [代码hello_window.cpp][https://github.com/timtingwei/LearnOpenGL/src/1.getting_started/1.1.hello_window/hello_window.cpp]
```sh
$ g++ -std=c++11 hello_window.cpp -lGLEW -lGL -lGLU -lglfw3 -lX11 -lXxf86vm -lXrandr -lpthread -lXi -ldl -lXinerama -lXcursor
$ ls
a.out  hello_window.cpp
$ ./a.out
Starting GLFW context, OpenGL 3.3
```
看到初始化窗口，代表成功。

**编译后窗口透明问题**
虽然窗口初始化成功, 但不知道我的窗口为什么是透明的, 难道是ubuntu的原因吗？
Render部分再加入两行代码
```cpp
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);
```

**编译后libGLEW.so.2.1: No such file or directory 问题**

```sh
..
$ ./a.out
./a.out: error while loading shared libraries: libGLEW.so.2.1: cannot open shared object file: No such file or directory
```
查询[stackoverflow libGLEW相关问题][5]
得到libGLEW在lib64下, 于是便创建软link
$ sudo ln -s /usr/lib64/libGLEW.so.2.1 /usr/lib/libGLEW.so.2.1

==========

refwebsite:
https://learnopengl-cn.readthedocs.io/zh/latest/01%20Getting%20started/02%20Creating%20a%20window/
https://wiki.ubuntu-tw.org/index.php?title=Howto_Install_OpenGL_Development_Environment
https://stackoverflow.com/questions/17768008/how-to-build-install-glfw-3-and-use-it-in-a-linux-project
http://www.glfw.org/docs/3.0/quick.html
https://stackoverflow.com/questions/26372359/error-loading-shared-library-glew
http://www.glfw.org/download.html

[1]:https://learnopengl-cn.readthedocs.io/zh/latest/01%20Getting%20started/02%20Creating%20a%20window/
[2]:https://wiki.ubuntu-tw.org/index.php?title=Howto_Install_OpenGL_Development_Environment
[3]:https://stackoverflow.com/questions/17768008/how-to-build-install-glfw-3-and-use-it-in-a-linux-project
[4]:http://www.glfw.org/docs/3.0/quick.html
[5]:https://stackoverflow.com/questions/26372359/error-loading-shared-library-glew
[6]:http://www.glfw.org/download.html
