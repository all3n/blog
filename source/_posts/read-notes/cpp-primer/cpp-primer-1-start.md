---
layout: post
title: cpp-primer-1:cpp quick start
date: 2017-04-20 14:01:00
tags:
    - cpp
    - cpp-primer
    - read-notes
---
# Start

## 第一个C++ 程序
> hello-world.cpp 
<!--more-->
```
#include <iostream>
using namespace std;
int main(){
    cout<<"hello world!"<<endl;
    return 0;
}
```

1. 入口函数为main，前面int代表返回类型，return 0 代表程序成功执行
2. #include 预处理器指示符
3. **using namespace std**代表使用std命名空间
4. C++ 每一条语句后面都是需要加分号; 
5. cout 写出到标准输出 cout 是在std 命名空间下的

## 编译执行
1. 编译: g++ -o hello-word hello-world.cpp
2. 执行程序: ./hello-world 结果如下

> hello world!

## 预处理指示符
1. 使用**#**开头
2. #define 
    1. 定义常量
        1. #define XX 1
    2. 避免include 被重复引入
        比如在a.h加入 在首次引入之后A_H就会被定义，第二次引入ifndef A_H 条件就会为false
        
        ```
        #ifndef A_H
        #define A_H
        ...
        #endif
        ```
    3. 其他常量
        1. __LINE__ 当前行
        2. __FILE__ 当前文件名

## 注释
1. 注释对 /* */
2. 行注释 //

## 输入输出
1. iostream 库提供
    1. [cout](http://en.cppreference.com/w/cpp/io/cout) 终端输入：标准输入
    2. [cin](http://en.cppreference.com/w/cpp/io/cin) 终端输出：标准输出 回车,TAB,空格 会被当做结束标识
    3. cerr 标准错误输出
1. endl 换行符 '\n'
1. 操作符
    1. ```<<``` 将一个值导向输出流cout/cerr
    2. ```>>``` 从一个流输出一个变量上

1. 文件流操作
    1. [fstream](http://en.cppreference.com/w/cpp/io/basic_fstream) 提供 操作与标准输出类似
    1. 文件输出流 ofstream("filename")
    2. 文件输入流 ifstream("filename")


