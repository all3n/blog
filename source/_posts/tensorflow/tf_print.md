---
title: Tensorflow print
date: 2019-09-27 19:39:10
tags:
    - tensorflow
category:
    - tensorflow
    - framework
---
## [tf.Print](https://www.tensorflow.org/api_docs/python/tf/Print?hl=en)
 该op已经deprecated,目前已经不建议使用
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

def print_op(*inputs , **kwargs):
    print_ops.append(tf.print(*inputs, **kwargs))

x = tf.range(10)

print_op("x", x)
print_op("x_no_summarize", x, summarize = -1)
print_op("x_output_stream", x, output_stream = sys.stdout)

# tf1.13开始支持 文件 file:// 开头
print_op("x_file", x, output_stream = "file:///tmp/debug.log")

with tf.Session() as sess:
    with tf.control_dependencies(print_ops):
        b = x * 3
    print(sess.run(b))

```

> x [0 1 2 ... 7 8 9]
> x_no_summarize [0 1 2 3 4 5 6 7 8 9]
> x_output_stream [0 1 2 ... 7 8 9]
> [ 0  3  6  9 12 15 18 21 24 27]

```
cat /tmp/debug.log                                                              
x_file [0 1 2 ... 7 8 9]
```

## tf.print eager 模式
1. 单个tensor
```
tf.compat.v1.enable_eager_execution()
tensor = tf.range(10)
tf.print(tensor, output_stream=sys.stderr)
```

2. 多个tensor
```
tf.compat.v1.enable_eager_execution()
tensor = tf.range(10)
tf.print("tensors:", tensor, {2: tensor * 2}, output_stream=sys.stdout)
```

3. 函数内
```
import tensorflow as tf
import sys
tf.enable_eager_execution()

@tf.contrib.eager.defun
def f():
    tensor = tf.range(10)
    tf.print("debug:", tensor, output_stream=sys.stderr)
    return tensor

range_tensor = f()
```

