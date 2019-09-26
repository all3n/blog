---
layout: post
title: cpp-primer-6-function
date: 2017-04-20 14:01:00
tags:
    - cpp
    - cpp-primer
    - read-notes
category:
    - read-notes
    - cpp-primer
---


# Function
1. function
    1. type funname(type arg1,...)
2. 函数指针 函数名就是函数指针
    
    ```
    int ab(int x);
    int (*p)(int) *p = ab;
    typedef int (*p_ab)(int);
    p(12);
    (*p)(12);
    ```

<!--more-->

3. 函数指针数组  
    1. int (*p[5])(int) *parr;  

