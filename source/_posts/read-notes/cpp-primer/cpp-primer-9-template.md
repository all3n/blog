---
layout: post
title: cpp-primer-9:template
date: 2017-04-21 14:01:00
tags:
    - cpp
    - cpp-primer
    - read-notes
---


# template
1. 示例

```
template <class TYPE>
TYPE min(TYPE a,TYPE b){
    return a < b ? a : b;
}
```
<!--more-->
2. 模板参数
    1. 模板类型参数
        1. class 或者typename 加一个标识符
    2. 模板非类型参数
        1. 由普通参数声明构成，代表常量表达式
        
        ```
        template <typename Type, int size>
        Type min(Type (&r_array)[size]){}
        ```
1. 显式模板
    1. vector\<int\>


