---
layout: post
title: cpp 常用命令
date: 2018-04-20 14:22:41
tags:
    - cpp
category:
    - cpp
    - lang
---

# cpp 常用命令
## basic
1. compile
    1. g++ -c -o a.o a.cpp
        1. -I includeDir
        1. -L libraryDir
        1. -lxx sharead xx library
        1. lxx static xx library
<!--more-->
1. create static library
    1. ar ru libxxx.a a.o b.o c.o
1. create shared library
    1. Linux: g++ --shared -fPIC -o libxxx.so a.o b.o c.o
    1. MacOs: g++ -dynamiclib -fPIC -o libxxx.dylib a.o b.o c.o
1. show lib dependency
    1. Linux:ldd
    1. MacOs:otool -L
1. include path:echo | g++ -v -x c++ -E -
1. /etc/ld.so.conf
1. ldconfig -p
