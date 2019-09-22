---
layout: post
title: pig basic
date: 2018-03-19
tags:
    - pig
category:
    - bigdata
---

# Pig Basic

[Pig 官网](http://pig.apache.org/)
Apache Pig 是一个高级过程语言，适合于使用 Hadoop 和 MapReduce 平台来查询大型半结构化数据集。
通过允许对分布式数据集进行类似 SQL 的查询，Pig 可以简化 Hadoop 的使用
pig 语法关键字是不分大小写


##  Pig启动模式
1. local
2. mapreduce
3. spark
通过 pig -x local xxx.pig 指定

### 本地测试

本地一个目录先创建一个文件比如data.txt
```txt data.txt
w,10,6
h,12,2
h,14,1
```
pig -x local 进入交互模式 (下面省略中间执行日志)
```pig
grunt> DATA = LOAD 'data.txt' USING PigStorage(',') AS (name: chararray, age:int, num: float);
grunt> DUMP DATA;
(w,10,6.0)
(h,12,2.0)
(h,14,1.0)
grunt> describe DATA;
DATA: {name: chararray,age: int,num: float}
```

### 执行参数
1. [Properties](http://pig.apache.org/docs/r0.17.0/start.html#properties)
通过-D:  
等价设置环境变量PIG_OPTS, export PIG_OPTS=–Dmapreduce.task.profile=true
或者通过文件
pig -P mypig.properties

常见mapreduce参数设置 hadoop相关配置
```bash
pig -Dmapreduce.job.cache.files="/user/hadoop/dict.txt#dict.txt" xxx.pig
```

2. 变量 -p -param 替换pig文件中变量
pig -p INPUT="data.txt" xx.pig

### log4j 配置
默认是INFO 日志特别多，可以通过log4j文件改成ERROR
```property pig-log4j.properties
log4j.rootLogger=ERROR,stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n
```
pig -log4jconf ./pig-log4j.properties -x local

2. [set](http://pig.apache.org/docs/r0.17.0/cmds.html#set)
 主要用于job名称 job并行度 等job相关设置

```pig
SET job.name 'my job';
SET default_parallel 100;
```
## PigLatin
pig 文件是使用PigLatin语法描述
注意pig里 字符串都是 单引号

### 结构
1. tuple
    有序字段集合
    (name, 18)
2. bag
    tuple 的组合
3. [map](http://pig.apache.org/docs/r0.17.0/basic.html#map-schema)
    key/value pair
    [ key#value <, key#value …> ]
    [name#John,phone#5551212]

###  引入包

```pig
REGISTER 'hdfs://hadoop/piglibs/*.jar';
```

### 加载数据
pig记载数据通过[Loader](http://pig.apache.org/docs/r0.17.0/func.html#load-store-functions)方式

apache内置多种Loader
1. TextLoader
load 一行为一个字段
默认TextLoader是文本TSV分割数据(支持gzip,bzip)
TSV: 分割
A = LOAD 'student' USING PigStorage('\t') AS (name: chararray, age:int, gpa: float);
2. JsonLoader 官方这个不是太好用 不支持 深层次json
a = load 'a.json' using JsonLoader('a0:int,a1:{(a10:int,a11:chararray)},a2:(a20:double,a21:bytearray),a3:[chararray]');  

第三方Loader
twitter elephantbird
1. sequenceFileLoader

```
pairs = LOAD '$INPUT' USING com.twitter.elephantbird.pig.load.SequenceFileLoader (
'-c com.twitter.elephantbird.pig.util.IntWritableConverter',
'-c com.twitter.elephantbird.pig.util.TextConverter'
) as (
key: int,
value: chararray
);
```

2. Thrift Lzo(Block/Base64)

```pig
DATA = LOAD '$INPUT' 
  USING com.twitter.elephantbird.pig.load.ThriftPigLoader('com.xxx.ThriftClasss');
```
3. Protobuf

```pig
DATA = LOAD '$INPUT'
  USING com.twitter.elephantbird.pig.load.ProtobufPigLoader('com.xxx.protobufClass');
```
4. Json (elephant 版本支持嵌套)

```pig
DATA = LOAD '$INPUT'
  USING com.twitter.elephantbird.pig.load.JsonLoader('-nestedLoad');
```

注意 等号 左右 要有一个空格 不然会语法错误

```pig
INPUT_LOG = LOAD '$XXX_LOG' USING org.apache.parquet.pig.ParquetLoader();
```

###  遍历数据

```
INPUT_LOG = FOREACH INPUT_LOG GENERATE time, uid;
```

###  过滤数据
```
FILTERED_LOG = FILTER INPUT_LOG BY isValid == 1;
```
###  join 数据
1. [inner join](http://pig.apache.org/docs/r0.17.0/basic.html#join-inner)
alias = JOIN alias BY {expression|'('expression [, expression …]')'} (, alias BY {expression|'('expression [, expression …]')'} …) [USING 'replicated' | 'bloom' | 'skewed' | 'merge' | 'merge-sparse'] [PARTITION BY partitioner] [PARALLEL n]; 

inner join 和sql inner join一样只有两边都有 才保留
```pig
grunt> DATA = LOAD 'data.txt' USING PigStorage(',') as (name:chararray, age: int);
grunt> DUMP DATA;
(jake,10)
(lily,12)
(john,14)
(kite,13)
grunt> WEIGHT = LOAD 'weight.txt' USING PigStorage(',') as (name:chararray, weight: int);
grunt> DUMP WEIGHT;
(jake,40)
(lily,42)
(john,54)
(mike,64)
grunt> JOIN_DATA = JOIN DATA BY name,WEIGHT BY name;
grunt> DUMP JOIN_DATA;
(jake,10,jake,40)
(john,14,john,54)
(lily,12,lily,42)
grunt> DESCRIBE JOIN_DATA;
JOIN_DATA: {DATA::name: chararray,DATA::age: int,WEIGHT::name: chararray,WEIGHT::weight: int}
```
2. [outer join](http://pig.apache.org/docs/r0.17.0/basic.html#join-outer)
alias = JOIN left-alias BY left-alias-column [LEFT|RIGHT|FULL] [OUTER], right-alias BY right-alias-column [USING 'replicated' | 'bloom' | 'skewed' | 'merge'] [PARTITION BY partitioner] [PARALLEL n]; 

out join和sql outer join概念类似
分LEFT,RIGHT,FULL
分别是保留左边全部，保留右边全部，保留两边全部
LEFT,RIGHT,FULL [OUTER] OUTER 可以省略
```pig
grunt> JOIN_DATA = JOIN DATA BY name RIGHT, WEIGHT BY name;
grunt> DUMP JOIN_DATA;
(jake,10,jake,40)
(john,14,john,54)
(lily,12,lily,42)
(,,mike,64)

grunt> JOIN_DATA = JOIN DATA BY name LEFT, WEIGHT BY name;
grunt> DUMP JOIN_DATA;
(jake,10,jake,40)
(john,14,john,54)
(kite,13,,)
(lily,12,lily,42)

grunt> JOIN_DATA = JOIN DATA BY name FULL, WEIGHT BY name;
grunt> DUMP JOIN_DATA;
(jake,10,jake,40)
(john,14,john,54)
(kite,13,,)
(lily,12,lily,42)
(,,mike,64)
```
###  group 数据
alias = GROUP alias { ALL | BY expression} [, alias ALL | BY expression …] [USING 'collected' | 'merge'] [PARTITION BY partitioner] [PARALLEL n];

1. ALL, group ALL to one group
B = GROUP A ALL;
2. Field
B = GROUP A BY f1;
3. Tuple
B = GROUP A BY (f1, f2);

```
A = load 'student' AS (name:chararray,age:int,gpa:float);

DESCRIBE A;
A: {name: chararray,age: int,gpa: float}

DUMP A;
(John,18,4.0F)
(Mary,19,3.8F)
(Bill,20,3.9F)
(Joe,18,3.8F)

B = GROUP A BY age;
DESCRIBE B;
B: {group: int, A: {name: chararray,age: int,gpa: float}}
```



###  [存储](http://pig.apache.org/docs/r0.17.0/basic.html#store)
STORE alias INTO 'directory' [USING function];
和加载数据类似
分隔符 tsv: [PigStorage](http://pig.apache.org/docs/r0.17.0/func.html#pigstorage)
[AvroStorage](http://pig.apache.org/docs/r0.17.0/func.html#AvroStorage)

第三方:twitter
[elephant bird store](https://github.com/twitter/elephant-bird/tree/master/pig/src/main/java/com/twitter/elephantbird/pig/store)
* LzoJsonStorage
* SequenceFileStorage
* LzoThriftBlockPigStorage
* LzoThriftB64LinePigStorage
* LzoProtobufBlockPigStorage
* LzoProtobufB64LinePigStorage


STORE D INTO 'mysortedcount' USING PigStorage();

### 其他
1. 查看描述：
    DESCRIBE DATA; 
1. 打印（只建议在少数据量时候使用，大数据建议先LIMIT再DUMP）
    DUMP DATA;
1. LIMIT
    alis = LIMIT alias  n;
1. 排序    
    alias = ORDER alias BY { * [ASC|DESC] | field_alias [ASC|DESC] [, field_alias [ASC|DESC] …] } [PARALLEL n];
    X = ORDER A BY age DESC;
1. 采样 
    SAMPLE alias size;
    size 0~1
    X = SAMPLE A 0.01;

###  [常见函数](http://pig.apache.org/docs/r0.17.0/func.html)

###  UDF 用户自定义函数
全称 User Defined Functions
1. 第三方实用 UDF： [DATAFU](http://datafu.apache.org/)
2. [自定义UDF](http://pig.apache.org/docs/r0.17.0/udf.html)