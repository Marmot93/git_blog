---
title: Docker部分参数理解
date: 2018-02-24 14:47:27
tags: 运维
---

### docker pull 镜像名 从云端拉取镜像（建议翻墙）

### docker search 镜像名 查看云端镜像

### docker run 运行容器
    -t, --tty 指定一个终端  
        例：-t ubuntu /bin/bash  （制定bash为交互终端）
    -i, --interactive 交互
    -d, --detach 后台运行并且给出容器ID
    -w, --workdir 指定工作目录
    -p, --publish 端口映射
        例：-p 6000:5000（容器外外：容器内）
    -P, --publish-all 同上，默认对应无需指定
    
    用法示例：
    docker run -*(上述参数) 参数的内容 ... 镜像（image）命令
    docker run -d -p 5000:5000 training/webapp python app.py
    
### docker ps 列出容器信息
    -a, --all 列出所有容器
    -l, --latest 最后一个，不论状态
    -n, --last int 最后几个创建的
    -q, --quiet 已退出的容器，只显示ID
    -s, --size 列出大小
    用法示例：
    docker ps -a 列出所有容器
    docker ps 列出正在运行的容器
    
### docker kill 容器ID 停止容器
    例： docker kill $(docker ps -a -q) 杀死正在运行的进程
    
### docker restart 容器ID 启动已停止的容器

### docker rm 容器ID 移除容器

### docker logs -f 容器ID 查看容器日志