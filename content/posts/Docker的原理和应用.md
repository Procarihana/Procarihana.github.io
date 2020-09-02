---
title: "Docker的原理和应用"
date: 2020-09-01T18:41:11+08:00
draft: false
toc: false
images:
tags: [docker, redis,]
---
- 虚拟机有一套完整独立的操作系统，非常占内存和资源
## Docker
- 保证开发、测试、交付、部署的环境完全一致
- 保证资源的隔离
- - 通过fastjson 任意代码执行漏洞，通过java进程获得来操控别的进程从而过得Linux的控制权限。而Docker 资源隔离只能够获得Docker内的权限，对宿主机没有任何影响
- 启动临时的、用完即弃的环境，例如测试
- 迅速（秒级）超大规模部署和扩容
- - 可以迅速扩容大量的容器，且环境是一模一样的

## Docker 的基本概念
- 镜像 image
- - 一个预定好的模板文件，Docker 引擎可以按照这个模板文件启动无数个一模一样，互不干扰的容器
- - 像是一份清单一样，每次启动这个镜像就按照这份清单来启动
- 容器 container
- - 一台虚拟的计算机，拥有独立的：
>- 网络
>- 文件系统
>- 进程
- - 默认和宿主机不发生任何交互
>- 意味着数据是没有持久化的 

## Docker 指令
#### docker run : 启动一个全新的控制资源隔离的docker容器
- 一台独立的计算机
- 每个容器都有一个ID，支持缩写
- docker run -it <镜像名><镜像中要运行的命令和参数>
- - 交互式命令行，当前shell中运行。Ctrl-c退出（启动一个容器，并立刻进入内部进行操作）
- docker run -d
- - daemon模式，在后台运行  
- -- name 为容器指定一个名字
- -- restart=always 遇到错误子哦那个重启
- -v <本地文件>:<容器文件>:文件权限（ro、rw……）
- - 宿主机上的文件映射到docker容器里面，使得docker容器里也可以打开这个文件，如果文件在容器里发生改变，同样的宿主机读取时也会发生改变。权限是不强制定义。而dockerfile里也可以控制权限。
- -p <本地端口>:<容器端口>
- - 把宿主机和容器通过指定的端口链接起来
- - 能够实现控制多个数据库，达到分布式部署的目的
- -e NAME=VALUE
-  - 传递初始化的参数，指定参数后，在容器里面输入Name，就会获得相应的VALUE，或者通过相应的VALUE启动程序
- Docker exec（ute）
- - 指定目标容器，进入容器执行命令
>- docker run -it<目标容器ID> <目标命令（通常为bash）> （进入容器内部）
`docker exec -it my-sql bash`
>- 可以想为ssh
- - 调试、解决问题必备命令
#### Docker pull
- 不指定镜像版本就默认为最新的版本（latest）
- url（镜像仓库地址）/镜像名：tag
- - 如果不加url，就是去中央仓库里面寻找镜像，如果添加，就去这个指定的地址下载镜像
#### docker images ： 查看本地已有的镜像
- 下载一个指定的镜像，方便随时启动

#### Dockerfile
- 指定镜像如何生成
- 每个镜像都会有一个唯一的ID
- Dockerfile （在一个文件加里面生成一个text）
```
FROM ubuntu :16.04 (容器运行系统)

RUN apt-get update && apt-get install -y nginx (容器生成后运行内容)

RUN echo"Hahaha" > /user/share/nginx/html/index.html 

EXPOSE 80 （访问端口）
```
- `docker build .`
- - bulid 需要在存放dockerfile 文件里执行
- 完成后就可以在 docker images 里面找到刚才自己新建的镜像（没有名字和tag，但是有id），然后就可以run
#### 镜像分层 
两个镜像基本内容一致，只有某些部分不一样，就可以通过分层的镜像，从而使镜像能够共用一致的部分，再分别执行剩下的

#### Tag
- 不指定就是latest
- Docker运行的镜像要放到指定的仓库里面
- Tag 前面是url/域名，push、pull、run，就会把镜像自动解析到相应的仓库里面
`my.company.com/jenkins`

## Tip
- `$ docker run -it +启动不成功的镜像 ` 能够看到启动不成功的原因