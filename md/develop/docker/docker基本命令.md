---
title: docker基本命令
date: 2020/11/26 15:40:11
toc: true
tags:
- docker,develop
---


##### 运行image

```
docker run hello-world
```
<!--more-->
```bash
docker run -d -p 80:80 --restart=always  nginx:latest

# 参数说明： 
# run 启动某个镜像 名称就是nginx:latest
# -d 让容器在后台运行
# -p 指定端口映射，宿主机的80端口映射到容器的80端口
# --restart 重启模式，设置 always，每次启动 docker 都会启动 nginx 容器。
```

访问http://localhost:80



##### 进入容器 & kill 容器

```bash
docker exec -it 4591552a4185 bash
# exec 对容器执行某些操作
# -it 让容器可以接受标准输入并分配一个伪tty
# 4591552a4185 是刚刚启动的 nginx 容器唯一标记 通过docker ps 查看
# bash 指定交互的程序为 bash

docker kill 4591552a4185 
```

#####   退出容器

ctrl + D 对容器的直接修改不会持久保存，如果容器被删，数据也会跟着丢失。



#####  数据挂载

每次修改内容都需要手动进入容器，太过繁琐，并且👆提到了，对容器的直接修改不会持久保存，如果容器被删，数据也会跟着丢失。

Docker 提供数据挂载的功能，即可以**指定容器内的某些路径映射到宿主机器上**，修改命令，添加 `-v` 参数，启动新的容器。

```bash
docker run -d -p 80:80  -v ~/docker-demo/nginx-htmls:/usr/share/nginx/html/ --restart=always  nginx:latest
```



#### 删除container

先停止container

```
docker stop container_id
```

再删除container

```
docker rm container_id
```




