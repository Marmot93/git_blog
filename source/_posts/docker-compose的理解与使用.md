---
title: docker-compose的理解与使用
date: 2018-04-04 17:30:12
tags:
---

docker-compose的理解与使用

docker-compose是用来做docker集群管理的，也就是说我们可以通过它来管理docker

执行docker-compose up 会读取docker-compose.yml中的配置文件来拉去镜像并构建和运行容器
``` docker-compose.yml
version: "3"

services:
  #  启动容器的名字
  redis:
    # image意味着直接拉取对应的云端镜像来构建容器
    image: redis:3.2
    # 映射端口
    ports:
      - "6379:6379"
    # 映射内外文件
    volumes:
      - ./redis/conf:/usr/local/etc/redis/
      - ./redis/data:/data
      - ./redis/log:/data/log
    command: redis-server /usr/local/etc/redis/redis.conf --appendonly yes

  service:
    # 会调用 docker build -t Dockerfile来构建文件，默认是当前目录下
    build: .
    volumes:
      - .:/data/code/
    ports:
      - "18567:8567"
    dns:
      - 114.114.114.114
    # 关联到之前的redis容器
    depends_on:
      - redis
```

``` Dockerfile
# 基于某一个镜像
FROM ubuntu:16.04
# 容器内的环境设置
ENV TZ "Asia/Shanghai"
ENV TERM xterm

# Add
ADD deploy/sources.list /etc/apt/sources.list

# Packages
RUN apt-get update

# Language 语言设置
RUN apt-get install -y locales
RUN locale-gen en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8
ENV TZ=:/etc/localtime

# basics 安装基础服务
RUN apt-get install -y git wget curl bzip2

# PIP Mirror 挂载pip源
RUN mkdir -p /root/.pip/
ADD deploy/pip.conf /root/.pip/


# PIP Package Prerequisites python的基础环境
RUN apt-get install -y build-essential libmysqlclient-dev libjpeg-dev libpng-dev zlib1g-dev libtiff-dev libtiff5 libfreetype6 \
    libwebp-dev


# project dir 建立和设定工作目录
RUN rm -rf /data/ && mkdir -p /data/
WORKDIR /data/
# 安装 miniconda3并移除脚本安装文件
RUN curl -k https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh > /tmp/miniconda.sh && /bin/sh /tmp/miniconda.sh -b -f -p /data/miniconda3/ && rm -rf /tmp/miniconda.sh

# Requirements 安装python包
ADD requirements.txt /data/
RUN /data/miniconda3/bin/pip install --no-cache-dir -r /data/requirements.txt
RUN /data/miniconda3/bin/conda config --add channels conda-forge
RUN /data/miniconda3/bin/conda install -y icu pcre libxml2 libiconv openssl jansson ca-certificates
RUN /data/miniconda3/bin/conda install -y certifi || true
RUN /data/miniconda3/bin/conda install -y uwsgi

RUN rm -rf /data/code/ && mkdir -p /data/code/

ENTRYPOINT ["/data/miniconda3/bin/uwsgi", "--ini", "/data/code/deploy/uwsgi.ini"]
```