---
layout: post
title: socket http
date: 2018-06-26 18:00:11
tags:
    - cpp
    - socket
---

# httpd
## [socket](http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/sys_socket.h.html)
1. server = [socket](http://pubs.opengroup.org/onlinepubs/9699919799/functions/socket.html)(PF_INET, SOCK_STREAM, 0)
    1. memset
    2. struct sockaddr_in name;
        3. name.sin_family  = AF_INET;
        4. name.sin_port = htons(port);
        5. name.sin_addr.s_addr = htonl(INADDR_ANY);
   6. setsockopt(httpd, SOL_SOCKET, SO_REUSEADDR, &on, sizeof(on))) < 0
<!--more-->
2. bind(server,(struct sockaddr *)&name, & namelen)
    1. port 如果为0 会sin_port 设置随机端口
3. listen(server,backlog)
4. while accept client socket
    1. pthread_create client socket processs
5. close(server)


## http 协议
1. [RFC2616](https://tools.ietf.org/html/rfc2616)


