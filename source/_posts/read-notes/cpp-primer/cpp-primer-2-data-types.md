---
layout: post
title: cpp-primer-2:data-types
date: 2017-04-21
tags:
    - cpp
    - cpp-primer
    - read-notes
---

# 基本数据类型

## 常量
1. 文字常量 不可寻址 nonaddressable
2. 整数型文字常量
    1. 20 //十进制
    2. 020 //八进制
    3. 0x14 //十六进制
1. 缺省 默认有符号int
    1. 其他符号
    2. l/L long u/U unsigned f/F float
    3. e/E 科学计数法 1E-2
<!--more-->
4. 布尔常量
    1. true
    2. false
5. 字符常量
    1. 'a' '2'
    2. null 空字符
    2. 特殊字符 需要加\转义
        1. \n 换行
        2. \t 制表符
        3. \r 回车
        4. \' 单引号
        5. \\" 双引号
   6. 字符前面加L代表宽字符
        1. L"a"

## 变量
1. 变量是数据的名字的内存存储区，可以通过程序对其进行读写和处理。
1. 变量可寻址的 addressable
2. 左值(lvalue)：地址 位置值 (location value)
3. 右值(rvalue)：被读取的值 (read value)
1. 定义
    * 类型 变量名字
1. 变量名
    1. 标识符 identifier
    2. 特殊[keyword](http://en.cppreference.com/w/cpp/keyword) 不能用作变量名

1. 定义变量 是未初始化的，值是未定义是一个随机结果，一般建议初始化提供一个初始值。
2. 内置数据类型支持构造函数
    1. int ival = int();

## 指针类型
1. 指针中存有另一个对象的地址 使用*标识

```
    int *pi = 0;
    int pv = 3;
    pi = &pv;
```
3. 特殊支持 void *
    1. 不关心类型 只关心地址
    2. void *p = pi;
4. 获取指向内容 *p

## 字符串类型
1. c style string:const char * 
1. 使用c style string function:
    1. #include \<cstring\>
3. c++ style string
    1. #include \<string\>
4. init
    1. string a = "xxx";
    2. string a("xxx");
    3. string b(a); //copy a
    4. string b = a;
5. 操作
    1. 连接
    
    ```
    string c = a+b;
    ```
    1. 获取c style 字符串
    
    ```
    const char * str = s.c_str();
    ```
    1. 判断空
    
    ```
    if(s.empty()){
    }
    ```
    2. 获取大小 s.size()
    3. 替换
    
    ```
    replace(s.begin(),s.end(),',',' ')
    ```
    
## const 限定修饰符
1. 定义变量不可改变

    ```
    const char * XXX_BUF_SIZE = 512;
    ```

1. const 对象只能赋给 const 对象指针


## 引用类型
1. 引用类型 reference 有时候又称为别名 alias
    
    ```
    int ival = 1024;
    int &ival2 = ival;
    ```
1. const 引用可以使用不同类型变量初始化 一般用在函数参数上

## 布尔类型
1. true/false
2. 布尔类型可以隐式转换为int 1/0，算数值也可以，0或者空指针可以转换为false,其他类型的值可以转化为true

## 枚举类型
1. 一些列值的集合的类型
1. 比如XXX类型只能有3种类型值A,B,C
```
enum XXX{A,B,C};
```
1. 缺省情况 每个枚举值都被默认赋值 从0开始赋值 A=0,B=1,C=2
2. 也可以在声明是否制定其值


## 数组类型
1. 下标访问/索引访问 (下标从0开始)
2. 声明 
    1. int a[10];
    2. 显示指定 可以不写维度
        1. int a[] = {0,1,2};
    1. 字符串类型数组
        1. const char c1[] = {'c','p','p'};
        2. const char c2[] = "cpp";
        3. c1,c2 不等价 c2 包含一个终止空字符
    1. 初始化
        1. 不能引用数组
            [X] int & iar[] = {a,b,c};
        2. 不能数组初始化另一个数组
            2. [X] int arr[] =  ar;
    1. 多维数组
        1. int a[2][3]
        2. 用大括号方式初始化 其值都是0
    1. 数组 与 指针关系
        1. int ia[] = {1，2，3，4}；
        2. ia 就是数组第一个元素地址 &ia[0]
        
        
## [vector](http://en.cppreference.com/w/cpp/container/vector) 容器类型
1. include\<vector>
2. 定义指定长度vector
    1. vector\<int> ivec(10);
    2. vector\<int> ivec(10 ,-1); //初始化10个元素 每个元素为-1
2. vector 遍历

    ```
    if(ivec.empty()){
        return;
    }
    for(int ix = 0; ix < ivec.size(); i++ ){
        cout << ivec[i] <<endl;
    }
    
    for(vector<int>::iterator it = ivec.iterator();
     it != ivec.end(); 
     i++ ){
        cout << * it <<endl;
    }
    
    
    ```       
    
    
    
## complex 复数
1. #include \<complex>
2. complex<double> p(0,7); //0+7i


## typedef 名字
1. 重新定义类型用于助记

    ```
typedef double score;
typedef vector<int> vec_int;    
    ```

## [volatile](http://en.cppreference.com/w/cpp/language/cv)
1. 当一个对象会在编译器控制或检测之外被改变时候，如被系统时钟更新的变量，那么该对象应该被申明成volatile，编译器的优化不能应用在volatile的对象

    ```
    volatile int xxx;
    ```


## [pair](http://en.cppreference.com/w/cpp/utility/pair)
1. #include \<utility>
2. pair<string,string> author("name","xxxx");
3. map 的元素


