---
layout: post
title: cpp Compile Issues
date: 2018-06-25 16:11:01
tags:
    - cpp
---

# cpp compile issues
## undefined reference to `clock_gettime'
    1. add -lrt (glibc < 2.17 ldd --version 查看)
    1. 如果glibc > 2.17 可以省略

<!--more-->
