---
title: 使用CMake构建Fortran工程实例
date: 2019-4-15 16:52:17
category: Note
---

使用`cmake`构建一个`Fortran`工程。有如下特点：源码统一放在src文件夹下、编译时通过-D指定编译器和编译版本（Release或者Debug）、默认编译器为`gfortran`默认编译版本为Release。

<!--more-->

## 工程结构

工程结构如下

```
.
├── CMakeLists.txt
├── README.md
└── src
    ├── CMakeLists.txt
    ├── a.f90
    └── b.f90
```

## 根目录下的`CMakeLists.txt`

笔记见文中注释

```cmake
CMAKE_MINIMUM_REQUIRED(VERSION 2.8.8)

# 设置默认Fortran编译器。
# 要放在PROJECT(${PRJ_NAME} Fortran)和ENABLE_LANGUAGE(Fortran)之前。
# 注意cmake变量名区分大小写，Fortran应为首字母大写。
IF(NOT DEFINED CMAKE_Fortran_COMPILER)
    SET(CMAKE_Fortran_COMPILER "gfortran")
ENDIF()

SET(PRJ_NAME "T1")
PROJECT(${PRJ_NAME} Fortran)
# ENABLE_LANGUAGE(Fortran)

# 根据不同编译器的ID设置编译选项。
IF(${CMAKE_Fortran_COMPILER_ID} STREQUAL "GNU")
    SET(CMAKE_Fortran_FLAGS "-Wall -cpp -fdefault-real-8 -std=f95")
ENDIF()
IF(${CMAKE_Fortran_COMPILER_ID} STREQUAL "Intel")
    SET(CMAKE_Fortran_FLAGS "-warn all -fpp -r8 -stand f95")
ENDIF()

# 这句要在PROJECT(${PRJ_NAME} Fortran)之后，否则CMAKE_BUILD_TYPE会被清空
IF(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE "Release")
ENDIF()
SET(CMAKE_Fortran_FLAGS_DEBUG "-O0 -g -ggdb")
SET(CMAKE_Fortran_FLAGS_RELEASE "-O3")

ADD_SUBDIRECTORY(src bin)
```

## `src`目录下的`CMakeLists.txt`

```cmake
SET (_source_file_list "")
FILE(GLOB _source_file_list *.f90)

SET_SOURCE_FILES_PROPERTIES(
  ${_source_file_list}
  PROPERTIES
  LANGUAGE Fortran
)

# Set up target for executable
ADD_EXECUTABLE(${PRJ_NAME}
  ${_source_file_list})

SET_PROPERTY(TARGET ${PRJ_NAME} APPEND PROPERTY
  LINK_LANGUAGE Fortran
  )
```



以后也许会更新用到lib的情况。