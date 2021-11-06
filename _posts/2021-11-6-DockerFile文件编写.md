---
layout: post
title: DockerFile 文件编写
categories: Docker
description: DockerFile 文件编写
keywords: Docker, DockerFile

---

# DockerFile

## 一、使用好处

- Dockerfile解放了手工操作很多步骤
- Dockerfile保证了环境的统一

## 二、什么是DockerFile

​		Dockerfile中文名叫**镜像描述文件**，是一个包含用于组合镜像目录的文本文档，也可以叫“脚本”。他通过读取Dockerfile中的指令安装步骤自动生成镜像。

## 三、DockerFile命令

### 3.1 镜像构建命令

```
docker build -t 机构/镜像名称<:tags> Dockerfile目录
# tags可以对镜像打版本标签  目录可以使用绝对和相对目录
```

### 3.2 基础命令

#### FROM

```
# 制作基准镜像
FROM 镜像
# 比如我们要发布一个应用到tomcat里，那么的第一步就是FROM tomcat
FROM tomcat<:tags>
```

#### WORKDIR

```
# 类似于Linux中的cd命令，但是他比cd高级的地方在于，我先cd，发现没有这个目录，我就自动创建出来，然后在cd进去
WORKDIR /usr/local/testdir
```

#### COPY

```
# 将1.txt拷贝到根目录下。它不仅仅能拷贝单个文件，还支持Go语言风格的通配符，比如如下：
COPY 1.txt /
# 拷贝所有 abc 开头的文件到testdir目录下
COPY abc* /testdir/
# ? 是单个字符的占位符，比如匹配文件 abc1.log
COPY abc?.log /testdir/
```

#### ADD

```
# 将1.txt拷贝到根目录的abc目录下。若/abc不存在，则会自动创建
ADD 1.txt /abc
# 将test.tar.gz解压缩然后将解压缩的内容拷贝到/home/work/test
ADD test.tar.gz /home/work/test
```

> - docker官方建议当要从远程复制文件时，尽量用curl/wget命令来代替ADD。因为用ADD的时候会创建更多的镜像层。镜像层的size也大。
> - 二者都是只复制目录中的文件，而不包含目录本身。
> - COPY能干的事ADD都能干，甚至还有附加功能。
> - ADD可以支持拷贝的时候顺带解压缩文件，以及添加远程文件（不在本宿主机上的文件）。
> - 只是文件拷贝的话可以用COPY，有额外操作可以用ADD代替。

#### ENV

```
# 设置环境常量，方便下文引用，比如：
ENV JAVA_HOME /usr/local/jdk1.8
# 引用上面的常量，下面的RUN指令可以先不管啥意思，目的是想说明下文可以通过${xxx}的方式引用
RUN ${JAVA_HOME}/bin/java -jar xxx.jar
```

> ENV设置的常量，其他地方都可以用${xxx}来引用，将来改的时候只改ENV的变量内容就行。

### 3.3 运行指令

#### 3.3.1命令格式

- SHELL命令格式

比如

```text
RUN yum -y install vim
```

- EXEC命令格式

比如

```text
RUN ["yum","-y","install","vim"]
```

> 也就是说shell会创建子进程执行，EXEC不会创建子进程。

#### RUN

RUN指令是在构建镜像时运行，在构建时能修改镜像内部的文件。

#### ENTRYPOINT

容器创建时执行，而不是镜像构建时执行。

在容器启动的时候执行此命令，且Dockerfile中只有最后一个ENTRYPOINT会被执行，推荐用EXEC格式。

#### CMD

- 容器启动时执行，而不是镜像构建时执行。
- 启动时额外添加命令，CMD将失效