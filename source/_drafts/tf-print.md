---
layout: post
title: Tensorflow Print
date: 2019-09-27 19:39:10
tags:
    - tensorflow
category:
    - tensorflow
    - framework
---
## [tf.Print](https://www.tensorflow.org/api_docs/python/tf/Print?hl=en)
 该op已经deprecated
 以前大部分使用tf.Print 打印变量类似一下代码
 ```python
 some_op = ...
 some_op = tf.Print(some_op, [tf.shape(some_op), "some_op:"])
 ...
 sess.run(some_op)
 ```
 这个API已经在新的里面被移除了,使用小写tf.print代替,但是操作方式有点差异



## [tf.print](https://www.tensorflow.org/api_docs/python/tf/print?hl=en)
tf.print
没有是一个print operation

```
import tensorflow as tf
import sys

print_ops = []

def print_op(*inputs):
    print_ops.append(tf.print(inputs))

x = tf.range(10)

print_op("x", x)

with tf.Session() as sess:
    with tf.control_dependencies(print_ops):
        b = x * 3
    print(sess.run(b))

```

>> ('x', [0 1 2 ... 7 8 9])
>> [ 0  3  6  9 12 15 18 21 24 27]

