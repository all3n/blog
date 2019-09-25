---
title: tensorflow custom op
tags: tensorflow
category: tensorflow
date: 2019-09-25 19:07:22
---
# tensorflow op

[官方说明](https://www.tensorflow.org/guide/extend/op)

## Example
1. ZeroOut

```cpp zero_out.cc
#include "tensorflow/core/framework/op_kernel.h"
#include "tensorflow/core/framework/shape_inference.h"

using namespace tensorflow;

REGISTER_OP("ZeroOut")
    .Input("to_zero: int32")
    .Output("zeroed: int32")
    .SetShapeFn([](::tensorflow::shape_inference::InferenceContext* c) {
      c->set_output(0, c->input(0));
      return Status::OK();
    });




class ZeroOutOp : public OpKernel {
 public:
  explicit ZeroOutOp(OpKernelConstruction* context) : OpKernel(context) {}

  void Compute(OpKernelContext* context) override {
    // Grab the input tensor
    const Tensor& input_tensor = context->input(0);
    auto input = input_tensor.flat<int32>();

    // Create an output tensor
    Tensor* output_tensor = NULL;
    OP_REQUIRES_OK(context, context->allocate_output(0, input_tensor.shape(),
                                                     &output_tensor));
    auto output_flat = output_tensor->flat<int32>();

    // Set all but the first element of the output tensor to 0.
    const int N = input.size();
    for (int i = 1; i < N; i++) {
      output_flat(i) = 0;
    }

    // Preserve the first input value if possible.
    if (N > 0) output_flat(0) = input(0);
  }
};

REGISTER_KERNEL_BUILDER(Name("ZeroOut").Device(DEVICE_CPU), ZeroOutOp);
```
* REGISTER_OP:定义OP
    * Input: "input_name: int32"
    * Output: "output_name: int32"
    * setShapeFn: 

[OpKernelContext](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/op_kernel.h) 访问输入input和分配输出
[Tensor->flat](https://www.tensorflow.org/api_docs/cc/class/tensorflow/tensor#flat)&lt;int32>() 获取内部存储指针
* 获取输入:

```
    const Tensor& input_tensor = context->input(0);
    auto input = input_tensor.flat<int32>();
```
* 输出:(这里输出输出shape使用输入shape一致)


```
    Tensor* output_tensor = NULL;
    OP_REQUIRES_OK(context, context->allocate_output(0, input_tensor.shape(),
                                                     &output_tensor))
    auto output_flat = output_tensor->flat<int32>();

```



* [TensorShape](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/tensor_shape.h)
* [Status](https://www.github.com/tensorflow/tensorflow/blob/master/tensorflow/core/lib/core/status.h)
* [Error构造函数](https://www.github.com/tensorflow/tensorflow/blob/master/tensorflow/core/lib/core/errors.h)


```cmake CMakeLists.txt
cmake_minimum_required(VERSION 2.8)

# get tensorflow include dirs, see https://www.tensorflow.org/how_tos/adding_an_op/
execute_process(COMMAND python3 -c "import tensorflow; print(tensorflow.sysconfig.get_include())" OUTPUT_VARIABLE Tensorflow_INCLUDE_DIRS)
execute_process(COMMAND python3 -c "import tensorflow as tf; print(' '.join(tf.sysconfig.get_link_flags()), end='')" OUTPUT_VARIABLE Tensorflow_LINK_FLAGS)
execute_process(COMMAND python3 -c "import tensorflow as tf; print(' '.join(tf.sysconfig.get_compile_flags()), end='')" OUTPUT_VARIABLE Tensorflow_COMPILE_FLAGS)


# C++11 required for tensorflow
set(CMAKE_CXX_FLAGS "-std=c++11 ${Tensorflow_COMPILE_FLAGS} ${CMAKE_CXX_FLAGS}")
set(CMAKE_SHARED_LINKER_FLAGS "-Wl,--no-as-needed ${Tensorflow_LINK_FLAGS} ${CMAKE_SHARED_LINKER_FLAGS}")

# if GCC > 5
# if (CMAKE_CXX_COMPILER_VERSION VERSION_GREATER 5.0 OR CMAKE_CXX_COMPILER_VERSION VERSION_EQUAL 5.0)
#   set(CMAKE_CXX_FLAGS "-D_GLIBCXX_USE_CXX11_ABI=0 ${CMAKE_CXX_FLAGS}")
# endif()


message(STATUS "CXX_FLAGS:${CMAKE_CXX_FLAGS}")
message(STATUS "SHARE_LINK_FLAGS:${CMAKE_SHARED_LINKER_FLAGS}")
message(STATUS "INCLUDE:${Tensorflow_INCLUDE_DIRS}")

# build the actual operation which can be used directory
include_directories(${Tensorflow_INCLUDE_DIRS})
add_library(zero_out SHARED zero_out.cc)
```

```bash
mkdir build
cmake ..
make
```

```python
import tensorflow as tf
s = tf.InteractiveSession()
zo = tf.load_op_library("./build/libzero_out.so") 

zo.zero_out([[1,2],[3,4]])                                                                       
    <tf.Tensor 'ZeroOut_4:0' shape=(2, 2) dtype=int32>

zo.zero_out([[1,2],[3,4]]).eval()                                                                

    array([[1, 0],
       [0, 0]], dtype=int32)

```