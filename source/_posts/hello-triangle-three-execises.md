---
title: hello-triangle-three-execises
date: 2017-12-25 14:56:13
tags: OpenGL
---

代码托管在:
https://github.com/timtingwei/LearnOpenGL/tree/master/src/1.getting_started


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
