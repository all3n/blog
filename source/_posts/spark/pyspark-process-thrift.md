---
layout: post
title: pyspark 解析thrift lzo
date: 2018-05-17 19:12:11
tags:
    - spark
    - pyspark
    - thrift
category:
    - bigdata
    - spark
---
# PySpark 解析Thrift Lzo 类型数据

1. 包依赖 libs
    1. elephant-bird-core-4.6.jar
    1. elephant-bird-hadoop-compat-4.6.jar
    1. libthrift-0.x.x.jar (x.x 换成你的版本)
1. python依赖 pkg 
    1. 依赖文件
        1. gen_py thrift 生成(如果有注释中文需要在生成代码头部加上 `#-*- coding: utf-8 -*` )
        1. six.py  (Copy From python site-package)
        1. thrift   (Copy From python site-package)
    1. 将python依赖打成zip 方便加载 zip -r ../pkg.zip .
<!--more-->
1. python 脚本 
利用elephantbird RawMultiInputFormat 获取thrift bytes内容然后通过python thrift deserialize 转换成python 对象

```python
from pyspark import SparkConf, SparkContext
import thrift
from gen_py.xxx.ttypes import ThriftObj
from thrift.TSerialization import deserialize
conf = SparkConf()
sc = SparkContext(conf = conf)

logpath="/youhdfspath/xxx.thrift.lzo"
log = sc.newAPIHadoopFile(logpath,'com.twitter.elephantbird.mapreduce.input.RawMultiInputFormat',
'org.apache.hadoop.io.LongWritable','com.twitter.elephantbird.mapreduce.io.BinaryWritable')

rdd = log.map(lambda x:deserialize(ThriftObj(),x[1]))
rdd.saveAsTextFile("testfile")
```

1. shell 脚本

```shell
export SCRIPT_HOME=$(cd "`dirname $0`/"; pwd)
NAME=test
OUTPUT=testfile


LIBS=`ls $SCRIPT_HOME/libs/*`
LIBSTR=""
for lib in $LIBS;do
  LIBSTR="$LIBSTR,$lib"
done

LIBSTR=${LIBSTR:1}

hadoop fs -rm -r -f $OUTPUT
spark-submit \
    --master yarn \
    --deploy-mode cluster \
    --jars $LIBSTR \
    --py-files $SCRIPT_HOME/pkg.zip \
    --name "[pyspark][$NAME]" \
    $NAME.py
```

