---
layout: post
title: Tensorflow Variable 变量
date: 2018-06-15
tags:
    - tensorflow
---
## Create
1. tf.get_variable  提供名称 和 shape
    1. my_variable = tf.get_variable("my_variable", [1, 2, 3])
    1. 指定初始化方法
        1. my_int_variable = tf.get_variable("my_int_variable", [1, 2, 3], dtype=tf.int32,
  initializer=tf.zeros_initializer)
    1. 通过tensor 初始化，不要指定形状
        1. other_variable = tf.get_variable("other_variable", dtype=tf.int32,
  initializer=tf.constant([23, 42]))


<!--more-->
## 变量集合
    1. 程序未链接时候创建 tf.Variable
    1. collections
        1. tf.GraphKeys.GLOBAL_VARIABLES 多个设备共享
        1. tf.GraphKeys.TRAINABLE_VARIABLES 将计算梯度的变量
        1. 如果不想参加训练 可以以下两种方式
            1. my_local = tf.get_variable("my_local", shape=(),
collections=[tf.GraphKeys.LOCAL_VARIABLES])
            1. my_non_trainable = tf.get_variable("my_non_trainable",
                                   shape=(),
                                   trainable=False)
    1. 添加 不需要显示创建
        1. tf.add_to_collection("my_collection_name", my_local)
    1. 获取集合变量
        1. tf.get_collection("my_collection_name")

## 指定变量放置设备
    1. 放在第2快GPU上
    ```
        with tf.device("/device:GPU:1"):
            v = tf.get_variable("v", [1])
    ```
    1. 分布式
    ```
    cluster_spec = {
        "ps": ["ps0:2222", "ps1:2222"],
        "worker": ["worker0:2222", "worker1:2222", "worker2:2222"]
        }
    with tf.device(tf.train.replica_device_setter(cluster=cluster_spec)):
        v = tf.get_variable("v", shape=[20, 20]) 

    ```

## 变量初始化
1. 变量必须初始化才能使用
    1. 显式初始化(自己创建图和会话)
        1. session.run(tf.global_variables_initializer()) 初始化 tf.GraphKeys.GLOBAL_VARIABLES
    1. tf.contrib.slim,tf.estimator.Estimator,Keras 等高级框架会自动初始化
1. 初始化指定变量
    1. session.run(my_variable.initializer)
1. 查询未初始化
    1. print(session.run(tf.report_uninitialized_variables()))

## Variable && get_variable
1. Variable 会处理冲突，get_variable 不会
1 .Variable 每次会重新创建对象 reuse 无关，get_variable 会之前对象返回

## Scope
1. tf.variable_scope
    1. 可以让变量有相同的命名，包括tf.get_variable得到的变量，还有tf.Variable的变量
1. tf.name_scope
    1. 为了管理变量
    1. 可以让变量有相同的命名，只是限于tf.Variable的变量
    1. get_variable 会忽略name_scope
