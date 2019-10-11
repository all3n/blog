---
title: JsonCpp Guide
p: cpp/libs/jsoncpp
date: 2019-10-11 10:06:12
tags:
    - cpp
category:
    - cpp
    - libs
---


[Github](https://github.com/open-source-parsers/jsoncpp)


## Documentation
* [Official ApiDoc](http://open-source-parsers.github.io/jsoncpp-docs/doxygen/index.html) 新版用法
* [jsoncpp WikiBook](https://en.wikibooks.org/wiki/JsonCpp) 该文档是老版本写法


## Install
1. Build From Source
```
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/opt/cpplibs ..
make
make install
```
2. 使用包管理工具 缺点不能灵活控制版本
apt-get install libjsoncpp-dev

## Examples
```cpp json_v1.cpp
#include <iostream>
#include <json/json.h>
#include <string>


using namespace std;

int main(int argc, char* argv[])
{
    string jsonStr = "{\"name\": \"xxx\", \"array\": [\"a\", \"b\", \"c\"], \"obj\": {\"k\" : \"v\"}}";
    Json::Reader reader;
    Json::Value value;
    if (reader.parse(jsonStr, value)) {
        // read str field
        cout<<value["name"].asString()<<endl;
        // read array field
        for(int i = 0; i < value["array"].size(); i++){
            cout<<i<<":"<<value["array"][i].asString()<<endl;
        }
        // read obj field
        cout<<value["obj"]["k"].asString()<<endl;
    }
    return 0;
}
```

compile:
g++ -I/opt/cpplibs/include/ -o json_v1 json_v1.cpp /opt/cpplibs/lib/libjsoncpp.a && ./json_v1

output
```
json_v1.cpp: In function ‘int main(int, char**)’:
json_v1.cpp:20:18: warning: ‘Reader’ is deprecated: Use CharReader and CharReaderBuilder instead [-Wdeprecated-declarations]
     Json::Reader reader;
                  ^~~~~~
In file included from /data/cpplibs/include/jsoncpp/json/json.h:11:0,
                 from json_v1.cpp:10:
/data/cpplibs/include/jsoncpp/json/reader.h:35:83: note: declared here
 class JSONCPP_DEPRECATED("Use CharReader and CharReaderBuilder instead") JSON_API Reader {
                                                                                   ^~~~~~
xxx
0:a
1:b
2:c
v
```


注意这里使用老版本reader api会引起警告


## Example New Api
```cpp json_v2.cpp
#include <iostream>
#include <json/json.h>
#include <string>
#include <memory>


using namespace std;

int main(int argc, char* argv[])
{
    string jsonStr = "{\"name\": \"xxx\", \"array\": [\"a\", \"b\", \"c\"], \"obj\": {\"k\" : \"v\"}}";

    Json::Value value;

    // parse
    JSONCPP_STRING errs;
    Json::CharReaderBuilder readerBuilder;
    std::unique_ptr<Json::CharReader> const reader(readerBuilder.newCharReader());
    bool res = reader->parse(jsonStr.c_str(), jsonStr.c_str() + jsonStr.length(), &value, &errs);

    if (res && errs.empty())
    {
        // read str field
        cout<<value["name"].asString()<<endl;
        // read array field
        for(int i = 0; i < value["array"].size(); i++){
            cout<<i<<":"<<value["array"][i].asString()<<endl;
        }
        // read obj field
        cout<<value["obj"]["k"].asString()<<endl;
    }else{
        cout<<"json parse fail:"<<errs<<endl;
    }
    return 0;
}
```
源码方式
g++ -I/opt/cpplibs/include/ -o json_v2 json_v2.cpp /opt/cpplibs/lib/libjsoncpp.a && ./json_v2

如果使用包管理
共享库方式编译
g++ -I/usr/include/jsoncpp -ljsoncpp -o json_v2 json_v2.cpp
静态库方式编译
g++ -I/usr/include/jsoncpp -ljsoncpp -o json_v2 json_v2.cpp /usr/lib/x86_64-linux-gnu/libjsoncpp.a

output 已经没有警告了

```
xxx
0:a
1:b
2:c
v
```

## [Json::Value](https://github.com/open-source-parsers/jsoncpp/blob/1.9.1/include/json/value.h#L176)
1. 判断类型 bool isXxx()
   * isNull
   * isBool
   * isInt
   * isInt64
   * isNumbeic
   * isString
   * isObject
   * isArray
1. 转换原生类型 asXxx()
   * asInt
   * asInt64
   * asBool
   * asDouble
1. 访问字段
   * [] 方式访问 val["field"]
   * key是否存在 isMember("field")
1. 转成格式化字符串
   * String toStyledString()
## 流操作
   * 读取: [Json::parseFromStream](http://open-source-parsers.github.io/jsoncpp-docs/doxygen/namespace_json.html#acfebeaf759a841173ddce34c4da22486)
   * 输出: [writeString](http://open-source-parsers.github.io/jsoncpp-docs/doxygen/namespace_json.html#afd767fe4c7e962d0ff3d1a6d1622619f)
```cpp
Json::StreamWriterBuilder wbuilder; 
wbuilder["indentation"] = "\t";
std::string document = Json::writeString(wbuilder, root);
Json::CharReaderBuilder rbuilder;
rbuilder["collectComments"] = false;
std::string errs;
bool ok = Json::parseFromStream(rbuilder, std::cin, &root, &errs);
```