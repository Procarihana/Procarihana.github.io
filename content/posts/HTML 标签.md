---
title: "HTML 标签"
date: 2020-07-04T17:11:23+08:00
draft: false
tags: [HTML]
---

 ## A标签

 - 作用
 - - 跳转外部页面
 - - 跳转内部锚点
 - - 跳转到邮箱或电话等
 ##### href
 - 超级链接
 ```html
    <body>
    <a herf = "https://google.com" target="_blank" download>超链</a>
    </body>
```
 ![](/href.jpg)
 ##### target
 - 在哪个窗口打开超级链接
 - - 在空白页打开超级链接
 - - 在
 ##### download
 ##### rel=noopener



## target
- 在哪个窗口打开超级链接
- - 在空白页打开
```html
<a href="//goole.com" target=_blank>top</a>
```
- - 在顶层打开链接（正常打开链接）--
<a href="//goole.com" target=_top>top</a>

- - 在本层打开（Google 不允许用iframe指向，所以打不开，如果可以打开，则可以再其他页面直接打开） 

 ## 如何在编辑过程中正确打开html
 - http-server
 - -  安装
 ```
 $ yarn global add http-server  
 ```
 - 启动并点击任一网址，要编写路径
 - -  . (空格点空格【可省略】)-c（缓存 ）-1 

        ![](/http-server.png)

 - - 点击其中一个网址
 - -  打开并添加路径（所在文件名）
 
        ![](/http-server2.png)

- parcel
- - 安装
```
$ yarn global add parcel
```
- - 启动
        ![](/parcel2.png)
 

