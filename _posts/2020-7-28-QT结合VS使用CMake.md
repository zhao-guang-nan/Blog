---
layout:     post
title:      QT结合VS使用CMake
subtitle:   QT的CMake多个工程使用
date:       2020-07-28
author:     zhaoguangnan
header-img: img/about_cpp_study.jpg
catalog: true
tags:
    - C++
    - QT
    - VS
---

## QT结合VS使用CMake

&emsp;这里简单介绍CMake在QT工程中使用demo，对于CMake就不多介绍，把他看成Linux下的make即可。具体CMake有什么用，就不在这里介绍了，网上开源项目基本都用到。

## 创建QT的多个工程。
&emsp;创建两个工程文件夹，一个是**projectOne**,另外一个是**projectTwo**，还有一个文件**CMakeLists.txt**,其中**projectOne**下有三个文件，分别是**my_lib_export.h、mytestclass.cpp、mytestclass.h**,而**projectTwo**里面只有一个**main.cpp**文件，其实这么做的目的是，我需要在**projectTwo**的**main.cpp**调用**projectOne**的动态库。

![aAPTtx.png](https://s1.ax1x.com/2020/07/28/aAPTtx.png)

## projectOne的代码说明
&emsp;针对于**my_lib_export.h**,主要是因为需要生成动态库或者静态库,所以这个文件是作为包含文件使用的。

```c++

#pragma once

#ifdef MY_LIB_SHARED_BUILD

#ifdef _WIN32

#ifdef MY_LIB_EXPORTS

#define MY_LIB_API __declspec(dllexport)

#else

#define MY_LIB_API __declspec(dllimport)

#endif  // MY_LIB_EXPORTS

#else

#define MY_LIB_API

#endif  // _WIN32

#else

#define MY_LIB_API

#endif  // MY_LIB_SHARED_BUILD

```

其中这个文件中，会区分Windows平台与其他平台，因为Windows平台与其他平台有一些区别。

&emsp;针对于**mytestclass.h**则是一个导出的类，主要是提供给别的工程调用的接口。

```c++
#ifndef MYTESTCLASS_H
#define MYTESTCLASS_H
#include "my_lib_export.h"
#include <QObject>
#include <QDebug>

class MY_LIB_API MyTestClass : public QObject
{
    Q_OBJECT
public:
    explicit MyTestClass(QObject *parent = nullptr);
    void print()const;

signals:

public slots:
};

#endif // MYTESTCLASS_H

```
&emsp;针对于**mytestclass.cpp**是接口的使用细节

```c++
#include "mytestclass.h"

MyTestClass::MyTestClass(QObject *parent) : QObject(parent)
{

}
void MyTestClass::print()const
{
    qDebug("i am MyTestClass.");
}

```

## projectTwo的代码说明

&emsp;这里只有一个**main.cpp**函数,主要是调用**projectOne**的接口

```c++
#include <iostream>
#include "mytestclass.h"
int main()
{
	MyTestClass item;
	item.print();
	std::cout<<"this is two project."<<std::endl;
	return 0;
}

```

## CMakeLists.txt的配置

&emsp;一些配置如下

```cmake
cmake_minimum_required(VERSION 3.5)
project(MyCmakeApp LANGUAGES CXX)
#这里要用绝对路径，否则容易找不到，测试到Windows下使用环境变量找不到,而在linux下，则是"/opt/Qt5.14.2/5.14.2/gcc_64",在Mac可以自行查看
MESSAGE("正在设置QT的路径，目前使用的是Windows下")
set(CMAKE_PREFIX_PATH "D:/SoftwareInstallPath/Qt/Qt5.8.0/5.8/msvc2015_64")
MESSAGE("QT的路径前缀为" ${CMAKE_PREFIX_PATH})
set(CMAKE_INCLUDE_CURRENT_DIR ON)
file(GLOB PROJECT_ONE_SRC_LIST "${PROJECT_SOURCE_DIR}/projectOne/*.cpp")
file(GLOB PROJECT_ONE_Head_LIST "${PROJECT_SOURCE_DIR}/projectOne/*.h")
file(GLOB PROJECT_TWO_SRC_LIST "${PROJECT_SOURCE_DIR}/projectTwo/*.cpp")
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

MESSAGE("启用文件夹生成模式")
SET_PROPERTY(GLOBAL PROPERTY USE_FOLDERS ON)
#设置新的文件夹
#target_compile_definitions(FmAlgorthm PUBLIC -DFMALGORTHM_LIB)
find_package(Qt5 COMPONENTS Widgets Core Gui Sql REQUIRED)



#创建第一个工程projectOne的动态库
MESSAGE("PROJECT ONE File list=== " ${PROJECT_ONE_SRC_LIST})
add_library(projectOne SHARED ${PROJECT_ONE_SRC_LIST})
#设置"my_lib_export.h"宏定义的开关
target_compile_definitions(projectOne PUBLIC -DMY_LIB_SHARED_BUILD)
target_compile_definitions(projectOne PRIVATE -DMY_LIB_EXPORTS)
target_link_libraries(projectOne PRIVATE Qt5::Widgets Qt5::Core Qt5::Gui Qt5::Sql)
MESSAGE("PROJECT ONE create finish...")


#创建第二个工程projectTwo主要是调用projectOne的动态库
MESSAGE("PROJECT Two File list=== " ${PROJECT_TWO_SRC_LIST})
include_directories("${PROJECT_SOURCE_DIR}/projectOne/")
MESSAGE("添加包含目录完成... "
add_executable(projectTwo
	${PROJECT_TWO_SRC_LIST}
)
target_link_libraries(projectTwo PRIVATE Qt5::Widgets Qt5::Core Qt5::Gui Qt5::Sql projectOne)
MESSAGE("PROJECT Two create finish...")
```

## Windows下与linux下的生成情况
&emsp;在Windows下生成情况,执行**cmake**命令，可以生成.sln的vs工程文件

![aAGzQg.png](https://s1.ax1x.com/2020/07/28/aAGzQg.png)

&emsp;在linux下生成情况，执行**cmake**命令，然后执行**make**命令，可以直接生成可执行文件

![aAJ4kq.png](https://s1.ax1x.com/2020/07/28/aAJ4kq.png)

## 相关的连接

[GitHub上的demo](https://github.com/zhao-guang-nan/QT_USE_CMake_DEMO)

[在 CMakeLists.txt 中添加编译动态库选项](https://www.jianshu.com/p/a5bf40a26112)





