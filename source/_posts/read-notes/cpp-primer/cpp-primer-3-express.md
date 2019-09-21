---
layout: post
title: cpp-primer-3:expression
date: 2017-04-21
tags:
    - cpp
    - cpp-primer
    - read-notes
---

# 表达式

## 定义
1. 由一个或多个操作数构成，应用在操作数是叫做操作符
2. 在一个操作数上交一元操作符（&，*），作用在两个操作数上的叫做2元操作符（+，-）

<!--more-->
## 算术操作符

| 操作符 | 功能 | 用法 |
| --- | --- | --- |
| * | 乘 | a*b |
| /  | 除 | a/b |
| % | 求余 | a%b |
| + | 加 | a+b |
| - | 减 | a-b |


## 关系操作符

| 操作符 | 功能 | 用法 |
| --- | --- | --- |
| ! | 非 | !a |
| <  | 小于 | a<b |
| <= | 小于等于  | a<=b |
| > | 大于 | a>b |
| >= | 大于等于 | a>=b |
| == |等于|a == b|
| != |不等于|a!=b|
| && |逻辑与|a && b|
| \|\| |逻辑或|a\|\|b|

>关系操作符的结果都是布尔值

## 赋值操作符 = 

```
int a = 123;
a = 5;
```
> 高精度转低精度会进行隐式类型转换，会损失精度
> 复合赋值 a+=b

## 递增 ++ 递减 --
1. a++  返回a  a值加1 // ++ 在后 返回原结果
2. ++a 返回a+1 a值加1 // ++ 在前 返回结算后结果


## 条件操作符
```
bool isEqual = !strcmp(str1,str2)? true : false;
```

## sizeof 操作符
1. 返回字节长度
2. 用法 object 时候可以省掉括号
    1. sizeof (type)
    2. sizeof (object)
    3. sizeof object
3. 示例

    ```
int ia[] = {0,1,2};
size_t array_size = sizeof ia;
size_t ele_size = array_size / sizeof(int); 
    ```

    *注意:* size(ptr) 返回是指针长度 而不是指针指向的长度

1. string 类型 sizeof 与内容无关

## new/delete 操作符
1. 系统为每个程序运行时候提供可用的内存池，这个被称为**空闲存储区**(free store)或**堆**(heap)
2. new 分配对象 返回对象指针
2. delete 作用在对象指正上，如果在对数组类型需要加[]

    ```
 int * p = new int[10];
 int * pi = new int(100);
 delete [] p;
 delete pi;
    ```

## 逗号操作符
1. 从左往右计算
2. 结果为最右边表达式的值


## 位操作符

| 操作符 | 功能 | 用法 |
| --- | --- | --- |
| ~ | 按位非 | ~a |
| <<  | 左移 | a << b |
| >> | 右移  | a >> b |
| & | 按位与 | a & b |
| \| |  按位或 | a \| b |
| ^ |异或|a == b|
| &= |按位与等于|a&=b|
| \|= |按位或等于|a \|= b|
| \^= |按位异或等于|a\^=b|

## 类型转换
1. 显式类型转换(强制转换)
    1. static_cast
        1. int ival = static_cast<int>(4.14)
    1. dynamic_cast
    2. const_cast
    3. reinterpret_cast
2. 隐式类型转换
    1. 算术转换
        1. 小类型可以提升大类型 int->double
        2. 高精度转低精度 会有警告 不支持舍入 double->int
        1. 3.14L + 'a' // 'a'会转成asic 码


