---
layout: post
title: cpp-primer-7:class
date: 2017-04-20 14:01:00
tags:
    - cpp
    - cpp-primer
    - read-notes
---


# 类 Class
## 定义
类定义之后必须接一个**分号**或者**声明**

```
class ClassName{
};

class ClassName{
} a b;
```
<!--more-->
## 数据成员
默认都是非静态，不能显式初始化
静态可以初始化(类共享)

```
class A{
    int a;
    string b;
    static const int _SIZE = 100;
};
```

## 成员函数
类中定义函数声明，也可以函数定义

```
class A{
    void hello();
    void hello2(){
        cout<<"hello"<<endl;
    }
}

```

## 访问
1. public
    
    ```
    A a;
    a.name;
    
    A * pa = new A;
    pa->name;
    ```
    
    ```
    //访问静态
    A::static_field;
    ```

2. private 类私有
3. protected  派生类共享

## 友元函数
1. friend 不受 public,private,protected 影响
2. 使得某个函数可以访问

## 声明
1. class A; //只声明没有定义


## 构造函数 没有返回类型
构造函数不能为const或者volatile

```
class Screen{
public:
    Screen(int h=8, int w=12,xx="afd"):
    _height(h),
    _width(8){
    
    }
}

使用缺省构造函数不需要括号

// error Screen a(); 编辑器会认为返回Screen的函数
Screen a;

```

## const volatile
1. const
    1. 定义了就不可以修改
    3. 类成员函数const 不可以修改成员
    
    ```
    class A{
        void xxx() const;
    };
    ```
2. volatile 编译器无法控制或者监测的

## this 指针
1. 指向对象本身
2. 在需要返回对象自身时候使用
3. 返回引用  return *this;
4. 返回指针 return this;

## 静态成员
1. 没有this指针
2. 类定义时候初始化
3. A::field 访问

## 类成员指针
1. 指向类成员a的short 指针

```
short Screen::* p = &Screen::height;
```
1. 静态成员指针 类似普通指针

## union

```
union {
    int ival;
    char cval;
} uv;
```

## 嵌套类
1. Tree 嵌套Node, Tree::Node

```
class Tree{
public:
    class Node{}
    Node * tree;
};
```



## 拷贝构造函数
1. 区分普通类型和对象指针类型拷贝

## 析构函数
1. ~ClassName(); 无返回类型
2. delete 相关new开辟的空间
3. delete 时候不需要判断 是否!=0编辑器隐式执行
4. 不会删除不指向认为对象的指针

## 操作符重载
1. istream & operator >> (istream &,string &);
1. 左值 右值
2. 类操作符 省略左值


