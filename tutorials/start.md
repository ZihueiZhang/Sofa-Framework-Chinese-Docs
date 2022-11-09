# Sofa开发实战入门

## Q1：sofa用C++还是Python，用什么平台（IDE）来开发，需要学习哪些知识用于软件的开发

### 语言区别
#### C++开发
 - xml写场景
 - C++写组件
 - sofa核心代码+大部分插件是C++实现
 - 更新代码后需要再次编译
#### python开发
 - python写场景
 - python & C++ 写组件(也就是类)
 - 少部分组件是python实现的(softrobotics)
 - 一次编译后，C++代码不更新，就无需再编译
 - 后期方便与深度学习算法结合
### 语言选择
使用难度差不多，看项目需求。

#### 环境配置阶段，C++配置相对简单
  - C++和python版本都需要进行环境配置，但python会额外包括python相关库、python封装器的安装。
#### 初步使用阶段，难度差不多
 - 限于使用现有组件写场景
 - python&xml语法简单，写场景的难度都不高
#### 深度使用阶段，离不开C++
 - 涉及到优化组件，甚至自主开发组件/插件
#### Tips
**最理想的方式是两者都会,需要什么的时候用什么.**
**需求比较固定+只选其一的话就根据需求选择,看需求相关的插件组件是什么语言的**

### 开发环境
 - IDE: 主要看个人习惯，Clion、Visual Studio\pycharm，甚至python直接在命令行里跑都可以。
我使用的是Clion，集成式开发环境相对友好一些
 - 操作系统: 推荐Ubuntu(windows不是不能用,也能用)

### 软件开发知识(初步)
- 编程语言和思想。Python、C++，需要快速上手的话，可以先学基础语法及面向对象思想,类的继承\多态\,模板类，xml语法
- CMake用法:src_file\h_file\external_lib, cmake
- 设计模式:工厂模式
## Q2：推荐用sofa的哪个版本，有没有相关的学习资料（库函数文档/视频教程）
### 版本
 - 推荐21之后的版本
 - 新的版本会修复老版本的bug，理论上可以选择比较新的稳定版本

### 学习资料
 - sofa官方用户手册，https://www.sofa-framework.org/community/doc/
 - sofa github源码，https://github.com/sofa-framework/sofa
 - sofa github 用户手册文档https://github.com/sofa-framework/doc/
 - sofa应用 https://www.sofa-framework.org/applications/marketplace/
 - sofa论坛：老论坛（查资料）github新论坛（查资料+提问题）
 - sofa知乎专栏 https://www.zhihu.com/column/c_1464660014242103297
 - 库函数文档:https://www.sofa-framework.org/api/master/sofa/html/index.html（Doxygen导出的）
 - 视频教程: sofa week视频、SobotHub博主
 - sofa微信群

## Q3:对于柔性/连续体机器人仿真，有哪些常用的库函数
### plugin
● softrobotics（python）,柔性机器人
● beamadapter(C++),柔性管
### component
● beam，基础梁结构
其他
● soft-robotics tutorials, The Defrost Tutorials for Soft-robotics using Sofa.https://github.com/SofaDefrost/Tutorials

## Q4：用sofa进行仿真开发的思路、开发过程中会遇到的难题以及解决方法
### 基于sofa的仿真开发思路
1. 确定待仿真的目标场景是什么
2. 查找相关插件\组件,查找是否有相关的example场景，确定现有组件是否能满足需求
3. 是的话就直接编写场景文件，否的话就首先编写组件
4. 编写组件分为从0开始编写和在现有组件基础上进行修改或增加方法
5. 编写场景

### 开发中可能遇到的困难
#### 1. sofa框架代码量大，无法完全理解
     · 不需要一步到位，在实践中摸索
#### 2. 场景不知道怎么写/该用哪个组件/组件之间怎么搭配
     · 看sofa文档
     · 看example
     · 试
#### 3. 组件不知道有什么参数
     · 看类的构造函数
#### 4. 怎么新写一个组件
     · 学习sofa的代码框架:工厂模式\参数格式
     · CMakeList正确配置
#### 5. 运算速度的问题
     · 做并行化处理
#### 6. 源码bug
     · github上看bug是否已修复
     · 自行修复，然后可以在github上提交issue
