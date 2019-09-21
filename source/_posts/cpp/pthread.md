---
layout: post
title: pthread
date: 2018-06-26
tags:
    - cpp
---
#pthread

## 头文件
函数声明

```
#include<pthread.h>
int pthread_create(pthread_t *tidp,const pthread_attr_t *attr,
(void*)(*start_rtn)(void*),void *arg);
```

<!--more-->
##编译链接参数
-lpthread

## 返回值
若线程创建成功，则返回0。若线程创建失败，则返回出错编号，并且*thread中的内容是未定义的。
返回成功时，由tidp指向的内存单元被设置为新创建线程的线程ID。attr参数用于指定各种不同的线程属性。新创建的线程从start_rtn函数的地址开始运行，该函数只有一个万能指针参数arg，如果需要向start_rtn函数传递的参数不止一个，那么需要把这些参数放到一个结构中，然后把这个结构的地址作为arg的参数传入。
linux下用C语言开发多线程程序，Linux系统下的多线程遵循POSIX线程接口，称为pthread。
## 参数
第一个参数为指向线程标识符的指针。
第二个参数用来设置线程属性。
第三个参数是线程运行函数的起始地址。
最后一个参数是运行函数的参数。

## 注意事项
因为pthread并非Linux系统的默认库，而是POSIX线程库。在Linux中将其作为一个库来使用，因此加上 -lpthread（或-pthread）以显式链接该库。函数在执行错误时的错误信息将作为返回值返回，并不修改系统全局变量errno，当然也无法使用perror()打印错误信息。


