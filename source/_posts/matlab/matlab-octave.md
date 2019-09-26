---
layout: post
title: matlab 常用命令
date: 2016-03-20 13:11:15
tags:
    - matlab
category:
    - matlab
---


# matlab & ocatve
1. index 从1开始 
1. length 长度

## io
1. data = open("data.file");

<!--more-->


## cmd
1. clear ; close all; clc
    1. clc：清除命令窗口的内容，对工作环境中的全部变量无任何影响 
    1. close：关闭当前的Figure窗口 
    1. close all:关闭所有的Figure窗口 
    1. clear：清除工作空间的所有变量 
    1. clear all：清除工作空间的所有变量，函数，和MEX文
2. fprintf('hello world \n')
5. % comment


## convension
1. X   (m,n + 1) 
2. y    (m,1)
3. theta (n+1,1)


## matrix [Utility](Special-Utility-Matrices)
1. 列用;分割 行用,分割
1. a = [1;2;3]
2. X = data(:,1) 取第一列
3. X = data(:,[1,2]) 取1,2 列
1. 获取非0序号 [Find](https://octave.org/doc/interpreter/Finding-Elements-and-Checking-Conditions.html)
    1. pos = find(y==1)
2. size
    1. [m , n] = size(X)
2. X = [ones(m, 1) X];
1. 全0矩阵 zeros(x,y)
2. 全1矩阵 ones(x,y)
3. 单位矩阵 I = eye(n) Identity Martix
3. 矩阵赋值：assignment
    1. X(i,y) = n




## gen
1. linspace (BASE, LIMIT, N)
```  
    linspace(1,4,5)
    ans =
    1.0000   1.7500   2.5000   3.2500   4.0000
```
2. logspace(-2, 3, 5)
```
logspace(-2, 3, 5)
ans =
   1.0000e-02   1.7783e-01   3.1623e+00   5.6234e+01        1.0000e+03
```

## logic
1. for
```
 for i = 1:n
    do_something...
 end
```



## plot
1. figure;  % open a new figure window
2. [plot](https://octave.org/doc/interpreter/Two_002dDimensional-Plots.html#Two_002dDimensional-Plots)
3. 3D:surf(X,Y,V) 
4. 等高线: contour(X,Y,V,logspace(-2,3,20))



## [minimizers](https://octave.org/doc/interpreter/Minimizers.html#Minimizers)
1. options = optimset('GradObj', 'on', 'MaxIter', 400);
2. [theta, cost] = fminunc(@(t)(costFunction(t, X, y)), initial_theta, options);

