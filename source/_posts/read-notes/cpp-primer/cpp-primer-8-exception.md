---
layout: post
title: cpp-primer-8:exception
date: 2017-04-20 14:01:00
tags:
    - cpp
    - cpp-primer
    - read-notes
---

# exception 异常处理

```
try{
    xxxx
    throw X_Exception("");
}catch(E_Exception &e){
    cout<<e.what()<<endl;
}

```

<!--more-->
