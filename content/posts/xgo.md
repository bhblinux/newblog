+++
title = "xgo"
description = "Short description"
date = 2019-07-18T15:44:11+08:00
draft = false
+++

## xgo {#xgo}


## 交叉编译 {#交叉编译}


### 环境搭建 {#环境搭建}

1.  安装docker
2.  下载
3.  安装xgo镜像及编译指令
4.  配置环境变量


#### 1. 安装docker {#1-dot-安装docker}

平台选定为archlinux
`pacman -S docker`


#### 2. 下载go {#2-dot-下载go}


#### 3. 安装xgo docker镜像 {#3-dot-安装xgo-docker镜像}

xgo的docker镜像是我们真正的编译环境,[Github地址](https://link.jianshu.com/?t=https://github.com/karalabe/xgo)

1.  安装xgo docker镜像,这个镜像大概5.G, `docker pull karalabe/xgo-latest`
2.  安装xgo `go get github.com/karalabe/xgo`
3.  启动docker  `docker run -i -t xog-latest /bin/bash` 这里会要报错,关闭即可
4.  连接docker容器 `docker exec -it 容器的id /bin/bash`
5.  容器里面已经装好了xgo环境,直接git项目,编译即可