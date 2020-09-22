---
title: "Docker的原理和应用"
date: 2020-09-01T18:41:11+08:00
draft: false
toc: false
images:
tags: [docker, redis,]
---
- 虚拟机有一套完整独立的操作系统，非常占内存和资源,虚拟机内即使有空闲的资源也不能够集中利用，只能够浪费
- docker 启动的容器用于运行程序，剩余的内存能够继续用于新的容器，从而节省内存
- 阿里云的镜像加速所向无敌
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
- - 交互式命令行模式，当前shell中运行。Ctrl-c退出（启动一个容器，并立刻进入内部进行操作）
- docker run -d
- - daemon模式，在后台运行  
- -- name 为容器指定一个名字
- -- restart=always 遇到错误马上重启 -> 用于NGINX等
>- docker run --name springboot-nginx --restart=always -v `pwd`/nginx/nginx.conf:/etc/nginx/nginx.conf:ro -d -p 8085:80 nginx
- -v <本地文件>:<容器文件>:文件权限（ro、rw……）
- - 宿主机上的文件映射到docker容器里面，使得docker容器里也可以打开这个文件，如果文件在容器里发生改变，同样的宿主机读取时也会发生改变。权限是不强制定义。而dockerfile里也可以控制权限。
- - `pwd`当前路径 == 全路径
- -p <本地端口>:<容器端口>
- - 把宿主机和容器通过指定的端口链接起来
- - 能够实现控制多个数据库，达到分布式部署的目的
- -e NAME=VALUE
- - 传递初始化的参数，指定参数后，在容器里面输入Name，就会获得相应的VALUE，或者通过相应的VALUE启动程序
- - 例如Mysql 中利用 MYSQL_ROOT_PASSWORD=password 来传递密码、名字和database名字等等
#### Docker exec（ute）
- 指定目标容器，进入容器执行命令
>- docker run -it<目标容器ID/name> <目标命令（通常为bash）> （进入容器内部）
`docker exec -it my-sql bash`
>- 可以想为ssh(进入其他容器的内部进行操作)
- 调试、解决问题必备命令

#### docker logs 
- docker logs imageId或imageID
- - 查看目标容器的输出
- docker logs -f imageId或imageID

#### docker inspect imagesId
- 查看容器内的详细情况
- - 镜像生成的时间、id、内容等等

#### Docker pull
- 不指定镜像版本就默认为最新的版本（latest）
- url（镜像仓库地址）/镜像名：tag
- - 如果不加url，就是去中央仓库里面寻找镜像，如果添加，就去这个指定的地址下载镜像

#### docker push
- `docker push 地址/images:tag`
- 把镜像push到指定的地址，之后就可以在这个地址pull到自己的镜像
- 之后pull镜像的时候就是通过地址名字的tag来进行
#### docker images ： 查看本地已有的镜像
- 下载一个指定的镜像，方便随时启动

#### docker tag
- docker tag imagesID imagesName:tag 可以为镜像取一个名字和版本
- 名字前如果有地址，则会使得镜像在push 后，到达 指定的地址，要获得这个镜像就要从这个地址pull下来
## Dockerfile 创建镜像
- 每个镜像都会有一个唯一的ID
- 指定镜像如何生成

- Dockerfile （在一个文件加里面生成一个text）
```
FROM ubuntu :16.04 (容器运行系统)

RUN apt-get update && apt-get install -y nginx (容器生成后运行内容，执行脚本)

RUN echo"Hahaha" > /user/share/nginx/html/index.html 

EXPOSE 80 （访问端口）
```
- `docker build .`
- - bulid 需要在存放dockerfile 文件里执行
- 完成后就可以在 docker images 里面找到刚才自己新建的镜像（没有名字和tag，但是有id），然后就可以run （ID）
- docker tag imagesID imagesName:tag 可以为镜像取一个名字和版本

## 创建私服
- 可以在服务器里面布置私服
- docker run registry
- 私服的端口是5000 
- 服务器Id：5000/v2/ 查看私服是否部署成功
- docker tag 私服要运行的imagesID 服务器ID:5000/imagesName:tag
- - 更改images 的名字，从而表明images的地址
- docker push imageName:tag



## 镜像
- 可以任意对镜像进行tag
- - 决定了这个镜像会被push到哪⾥
- -  决定了从哪⾥下载镜像 
- 可以方便地创建镜像仓库的私服d
- - reigstry-mirror 加速国外访问的镜像
- - insecure-registry docker默认使用`https`访问如果是使用docker镜像服或者私服的话就需要添加，使用不安全的方式

- Tag 不指定就是latest
- Tag 如果前面是url/域名，push、pull、run，就会把镜像自动解析到相应的仓库里面
`my.company.com/jenkins`
- - Docker运行的镜像没有指定地址就从指定的中央仓库里面pull
#### 分层 
- Docker镜像默认是分层的，根据清单，在原有默认清单的基础上在添加需要的内容
- 两个镜像基本内容一致，只有某些部分不一样，就可以通过分层的镜像，从而使镜像能够共用一致的部分，再一层一层地分别执行剩下的。


## Kuberneted K8s
- k8s是一个需要跑在linxu宿主机上的软件，有一个master主节点，下面有多个物理服务器节点，节点里面有docker k8s控制节点的软件用于管理，提供网络的工具，Pob（容器）
- 能够让容器同一分级地自动化服务
- - Pob里面有一个或多个docker，出问题就会杀掉容器再重启
- - 升级的时候K8s能够自动杀掉部分容器升级，直到所有容器
- k8s容器出现问题是就杀掉重启，可以迅速部署大量的容器
- 而docker的容器出现问题是就要修复
## Tip
- `$ docker run -it +启动不成功的镜像 ` 能够看到启动不成功的原因


### Eg4
使用交互式命令行模式(-it)启动Docker容器。
向启动的Docker容器内挂载一个文件（卷），使得容器内能够读取到/app/config.txt文件，其内容为字符串"ABC"。
向启动的Docker容器内传递一个环境变量HCSP_ENV=DEF。
为启动的Docker容器设置要执行的命令：java Main。
docker run -it -e HCSP_ENV=DEF -v `pwd`/app/config.txt:/app/config.txt blindpirate/hcsp-quiz java Main

echo app/config.
#### 命令行
- ECHO 内容>>文本文件 新建本文写包含某些内容 

