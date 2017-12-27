---
title: 绘制三角形以及三个练习笔记 
date: 2017-12-25 14:56:13
tags: OpenGL
---

代码已托管在[/timtingwei/LearnOpenGL](https://github.com/timtingwei/LearnOpenGL/tree/master/src/1.getting_started)
	
# 图形管线渲染哪几个阶段？

顶点数据(Vertex data[])  ->
> 1, **顶点着色器**(Vertex Shader) ->
> 2, 形状(图元)装配(Shape Assembly) ->
> 3, **几何着色器**(Geometry Shader) ->
> 4, 光栅化(Rasterization) ->
> 5, **片段着色器**(Fragment Shader) ->
> 6, 测试与混合(Test And Blending)

三个着色器可以自己定义。

**顶点数据(Vertex data)：**
> * Vertex是一个3D坐标的集合
> * Vertex Data是用顶点属性(Vertex Attribute), 如位置Position, 颜色Color


**顶点着色器(Vertex Shader)：**
> * 把3D坐标转换成另外一种3D坐标
> * 允许对Vertex Attribute做基本处理

**片段着色器(Fragment Shader)：**
> * 计算一个像素最终的颜色
> * 着色器包含3D场景数据(光照，阴影，光的颜色)

**Alpha测试和混合(Blending):**
> * 测试深度，反应前后。
> * 检查Alpha并混合，(同个片段着色器，渲染多个三角形时候的颜色可能不同)


补充: 图元(Primitive):
> * 任何一个绘制指令的调用，都把图元传递给OpenGL。
> * 如GL\_POINTS、GL\_TRIANGLES、GL\_LINE_STRIP。


# 绘制一个三角形

### 顶点着色器
使用GLSL(OpenGL Shading Language)编写顶点着色器
```c
#version 330 core          // 每个着色器起始于版本声明, core代表核心模式

layout (location=0) in vec3 aPos;      // 设定输入变量的位置值

void main() {
  gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}

```

为什么需要**layout (location=0)**设定位置值？
> * **glVertexAttributPointer**函数第一个参数指定配置顶点属性，而**layout (location=0) 定义了顶点某变量的position属性。


相关内容in, out, uniform, type类型, 向量组合, main函数，会在下一节，[着色器中](https://timtingwei.github.io)讲解

### 编译顶点着色器

编写着色器源码后，
> 1, 先创建着色器对象;
> 2, 再把源码附加到着色器对象上;
> 3, 编译并检查

1, 创建顶点着色器对象
```cpp
unsigned int vertexShader;                         // ID引用
vertexShader = glCreateShader(GL_VERTEX_SHADER);   // 传递着色器类型参数
```

2, 附源码
```cpp
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
```

**glShaderSource**函数的几个参数：
> 0, 着色器对象
> 1, 传递的源码字符数量
> 2, 顶点着色器的真正源码
> 3, 先设置为NULL

3, 编译
```cpp
glCompileShader(vertexShader);
// 检查编译是否成功
int success;
char infoLog[512];
glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
// 如果不成功，用glGetShaderInfoLog获取信息, 存储再InfoLog中，打印
if {!success} {
  glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
  std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n" << infoLog << std::endl;
}
```

### 片段着色器
同理顶点着色器，编写着色器源码

```c
#version 330 core

out vec4 FragColor;

void main() {
  FragColor =  vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```

片段着色器只有一个vec4作为out, 表示最终的输出颜色, 最后一个值是alpha量

### 编译片段着色器
1, 创建对象
2, 附加源码
3, 编译并检查

自己重新写一遍, 并检查错误
1, 创建对象尝试
```cpp
unsigned int fragmentShader;
glCreateShader(fragmentShader, GL_FRAGMENT_SHADER);   // ERROR
```

1, **改正**
```cpp
unsigned int fragmentShader;
glCreateShader(GL_FRAGMENT_SHADER);     // DEBUG::不需要用着色器对象作为函数的参数，
```

查阅[khronos.org - glCreateShader](https://www.khronos.org/registry/OpenGL-Refpages/gl4/html/glCreateShader.xhtml)
> * Like buffer and texture objects, the name space for shader objects may be shared across a set of contexts, as long as the server sides of the contexts share the same address space. If the name space is shared across contexts, any attached objects and the data associated with those attached objects are shared as well.
会绑定上下文中的ID创建object, 而且同一ShaderType会被联系起来。


2, 附源码尝试
```cpp
glShaderSource(1, fragmentShader, fragmentSource, NULL)   // ERROR
```

2, 改正
```cpp
glShaderSource(fragmentShader, 1, fragmentShaderSource, NULL);  // DEBUG::交换参数位置
```

> 1. 着色器对象
> 2. 传递源码字符的数量

3, 尝试编译
```cpp
bool success;    // ERROR
glCompileShader(GL_FRAGMENT_SHADER, fragmentShader, success); // ERROR
unsigned char* infoLog[512];                                // ERROR
if (!success) {
  // 失败把报错内容储存再infoLog中输出
  glGetShaderinfoLog(fragmentShader, &success, &infoLog);   // ERROR
  std::cout << "COMPILE::ERROR::FRAGEMENT::SHADER" 
            << infoLog << std::endl;
}
```

3, 改正
```cpp
glCompileShader(fragmentShader);     // DEBUG::只要着色器对象一个参数
int success;                         // DEBUG:: success是一个int类型
char infoLog[512];                   // DEBUG:: char 并非 unsigned cha*
glGetShaderiv(fragmentShader, GL_COMPILE_STATUS, &success);   // DEBUG::获取success在先，才能对success做判断
if (!success) {
  // 失败输出
  std::cout << "ERROR::SHADER::FRAGMENT::COMPILATION_FAILED\n" << infoLog << std::endl;
}
```

### 创建着色器程序
绘制图元时，使用的是glUseProgram(shaderProgram), 因此，先要创建着色器程序

```cpp
unsigned int shaderProgram;
shaderProgram = glCreateProgram();
```


### 链接着色器对象
两个着色器对象要链接。
```cpp
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);
```

### 编译着色程序
与之前的着色器编译原理相同，只是调用函数和参数变量改变了。
```cpp
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
if (!success) {
  glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
  std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n"
              << infoLog << std::endl;
}
```

之后可以glUseProgram函数，激活程序对象。激活后，着色器调用和渲染调用都会使用这个程序对象。
### 删除着色器
完成以上几步后不要忘记删除着色器
```cpp
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```

### 输入顶点以及索引

```cpp
// 顶点输入
GLfloat vertices[] = {
  0.5f,   0.5f, 0.0f,    // 右上角
  0.5f,  -0.5f, 0.0f,    // 右下角
  -0.5f, -0.5f, 0.0f,    // 左下角
  -0.5f,  0.5f, 0.0f     // 左上角
};
```

**赋值array的时候, 不要忘记{};**

```cpp
// 顶点索引
GLuint indices[] = {
  0, 1, 3                // 第一个三角形
  // ...                 // 可根据vertices数组, 建立多组索引
}
```

### **创建和绑定顶点数组对象(VAO)**
VAO = Vertex Array Object

功能:
> * 随后的顶点属性调用都会储存在这个VAO之中。
> * 使得在不同顶点数据和属性设置之间切换变得简单。

**存储内容**:
> * glEnableVertexAttribArray和glDisableVertexAttribArray的调用。
> * 通过glVertexAttribPointer设置的顶点属性配置。
> * 通过glVertexAttribPointer调用与顶点属性关联的顶点缓冲对象。

使用注意:
> * **先VAO，再绑定和设置VBO, EBO 以及设置顶点属性指针**

```cpp
GLuint VAO;
glGenVertexArrays(1, &VAO);   // 创建VAO对象
glBindVertexArray(VAO);       // 绑定顶点数组对象
```


### **创建和绑定顶点缓冲对象(VBO)**

OpenGL如何解释顶点数据的内存，并指定其如何发送给显卡。

作用:
> * 通过VBO(Vertex Buffer Object)管理这个内存，它会在GPU内存中存储大量顶点。
> * 一次性发送大量数据到显卡上，而不是每个顶点发送一次。CPU发到显卡上速度慢，因此，一次发送多个比较好。而发送过去之后顶点着色器又能够立即访问。

<span style="color:blue">*我想这个过程应该是发生图形管线渲染的第一个阶段，是顶点数据和顶点着色器如何对接？就是依靠CPU发送到显卡上* </span>
```cpp
GLuint VBO;
glGenBuffer(1, &VBO);           // 创建
// 把顶点数组复制到缓存中提供OpenGL使用
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

### **索引缓冲对象(EBO)**

可以创建不同的索引数组，而使用同一个顶点数组。如下：
```cpp
unsigned int indices[] = { // 注意索引从0开始! 
    0, 1, 3, // 第一个三角形
    1, 2, 3  // 第二个三角形
};
```

```cpp
GLuint EBO;
glGenBuffer(1, &EBO);                // 创建
// 把顶点索引复制到缓存中提供OpenGL使用
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
```



### **设置顶点属性**
	
![vertex_attribute_pointer](/images/vertex_attribute_pointer.png)	

// 顶点属性默认是禁用的，要用glEnableVertexAttribArray函数开启。

输入的参数顶点属性的位置值。还记得之前的 <span style="color:red">**layout (location=0) in vec3 xx**</span> 吗？ 
### 绘制图元


glDrawArrays();
> * 使用当前绑定的顶点缓存对象进行绘制
glDrawElements()
> * 使用当前绑定的索引缓冲对象中的索引进行绘制

```cpp
glBindVertexArray(VAO);
// 使用当前绑定的顶点缓存对象进行绘制
glDrawArrays(GL\_TRIANGLES, 0, 3);
glBindVertexArray(VAO);
// 使用当前绑定的索引缓冲对象中的索引进行绘制
glDrawElements(GL_TRIANGLES, 6, GL\_UNSIGNED\_INT, 0);
```



-----

1.
添加加更多顶点到数据中，使用glDrawArrays，尝试绘制两个彼此相连的三角形

两种绘制方式的区别


2. 创建相同的两个三角形，但对它们的数据使用不同的VAO和VBO
// 放在同一个数组中，但需要各自绑定和各自绘制， 也可以匹配不同的顶点数组



3.创建两个着色器程序，第二个程序使用一个不同的片段着色器，输出黄色；再次绘制这两个三角形，让其中一个输出为黄色

// 放在同一个数组中，但需要各自绑定和各自绘制， 也可以匹配不同的顶点数组
// 注意：片段着色器不能放在数组里。
// 顶点着色器，片段着色器attach+link成program, 着色器需要创建，绑定源码，编译，检查编译四个步骤。

```cpp
// 片段着色器0
const GLchar* fragmentShaderSource0 = "#version 330 core\n"
    "out vec4 color;\n"
    "void main() {\n"
    "color = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"   // orange
    "}\n\0";

// 片段着色器1
const GLchar* fragmentShaderSource1 = "#version 330 core\n"
    "out vec4 color;\n"
    "void main() {\n"
    "color = vec4(1.0f, 1.0f, 0.0f, 1.0f);\n"   // yellow
    "}\n\0";
```


```cpp
// 创建片段着色器
  GLuint fragmentShaderOrange;
  GLuint fragmentShaderYellow;
  GLuint shaderProgramOrange;
  GLuint shaderProgramYellow;
  fragmentShaderOrange = glCreateShader(GL_FRAGMENT_SHADER);
  fragmentShaderYellow = glCreateShader(GL_FRAGMENT_SHADER);
  shaderProgramOrange = glCreateProgram();
  shaderProgramYellow = glCreateProgram();
  // ===========编译第一个片段着色器===================
  // 源码附加到着色器对象上
  glShaderSource(fragmentShaderOrange, 1, &fragmentShaderSource0, NULL);
  glCompileShader(fragmentShaderOrange);
  // 检测glComplieShader是否编译成功
  glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
  if (!success) {
    glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
              << infoLog << std::endl;
  }

  // ===========编译第二个片段黄色着色器===================
  // 源码附加到着色器对象上
  glShaderSource(fragmentShaderYellow, 1, &fragmentShaderSource1, NULL);
  glCompileShader(fragmentShaderYellow);
  // 检测glComplieShader是否编译成功
  glGetShaderiv(vertexShader, GL_COMPILE_STATUS, &success);
  if (!success) {
    glGetShaderInfoLog(vertexShader, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::VERTEX::COMPILATION_FAILED\n"
              << infoLog << std::endl;
  }
  // 把两个着色器对象链接到用来渲染的两个着色程序中
  glAttachShader(shaderProgramOrange, vertexShader);
  // =====连接第一个片段着色器=====
  glAttachShader(shaderProgramOrange, fragmentShaderOrange);
  glLinkProgram(shaderProgramOrange);
  glGetProgramiv(shaderProgramOrange, GL_LINK_STATUS, &success);
  if (!success) {
    glGetProgramInfoLog(shaderProgramOrange, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n"
              << infoLog << std::endl;
  }
  // =====连接第二个片段着色器=====
  glAttachShader(shaderProgramYellow, vertexShader);
  glAttachShader(shaderProgramYellow, fragmentShaderYellow);
  glLinkProgram(shaderProgramYellow);
  glGetProgramiv(shaderProgramYellow, GL_LINK_STATUS, &success);
  if (!success) {
    glGetProgramInfoLog(shaderProgramYellow, 512, NULL, infoLog);
    std::cout << "ERROR::SHADER::PROGRAM::LINKING_FAILED\n"
              << infoLog << std::endl;
  }
  // 删除着色器
  glDeleteShader(vertexShader);
  glDeleteShader(fragmentShaderOrange);
  glDeleteShader(fragmentShaderYellow);
```


=======

refwebsite:
[你好三角形 - learnopengl-CN](https://learnopengl-cn.github.io/01%20Getting%20started/04%20Hello%20Triangle/)
