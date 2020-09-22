---
title: "Cookie and Session"
date: 2020-09-12T16:08:36+08:00
draft: true
tags: ["Cookie","Session"]
---

## Session
一个用户的所有请求操作都应该属于同一个会话，而另一个用户的所有请求操作则应该属于另一个会话，二者不能混淆。
## Session 和 cookie
- 会话（Session）跟踪是Web程序中常用的技术，用来跟踪用户的整个会话。常用的会话跟踪技术是Cookie与Session。Cookie通过在客户端记录信息确定用户身份，Session通过在服务器端记录信息确定用户身份。
- Cookie就是这样的一种机制。它可以弥补HTTP协议无状态的不足。在Session出现之前，基本上所有的网站都采用Cookie来跟踪会话。
#### Session 和 cookie 区别
1. Cookie与Session的区别cookie数据存放在客户的浏览器（客户端）上，session数据放在服务器上，但是服务端的session的实现对客户端的cookie有依赖关系的；
2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗，考虑到安全应当使用session；
3. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能。考虑到减轻服务器性能方面，应当使用COOKIE；单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能超过3K；
## 什么是 Cookie？
> 1. Cookie 是浏览器访问服务器后，服务器传给浏览器的一段数据。
>2. 浏览器需要保存这段数据，不得轻易删除。
>3. 此后每次浏览器访问该服务器，都必须带上这段数据。
- Cookie 是一些数据, 存储于你电脑上的文本文件中。
- - 当 web 服务器向浏览器发送 web 页面时，在连接关闭后，服务端不会记录用户的信息。
- Cookie 的作用就是用于解决 "如何记录客户端的用户信息":
- - 当用户访问 web 页面时，他的名字可以记录在 cookie 中。
在用户下一次访问该页面时，可以在 cookie 中读取用户访问记录。
- 设置了新的 cookie，旧的 cookie 不会被覆盖。
- Cookies提供了一种在服务器和浏览器之间交换信息的方法，以管理会话（登录，购物车，游戏得分），记住用户首选项（主题，隐私策略接受）以及跟踪整个站点的用户行为。Cookies在一定程度上解放了服务器端的压力，因为将一部分数据放在浏览器端存储，所以这部分数据不能是涉及应用安全的数据。


## Cookie 作用
- 第一个作用是识别用户身份。
- - 比如用户 A 用浏览器访问了 http://a.com，那么 http://a.com 的服务器就会立刻给 A 返回一段数据「uid=1」（这就是 Cookie）。当 A 再次访问 http://a.com 的其他页面时，就会附带上「uid=1」这段数据。
- - 同理，用户 B 用浏览器访问 http://a.com 时，http://a.com 发现 B 没有附带 uid 数据，就给 B 分配了一个新的 uid，为2，然后返回给 B 一段数据「uid=2」。B 之后访问 http://a.com 的时候，就会一直带上「uid=2」这段数据。
- - - 借此，http://a.com 的服务器就能区分 A 和 B 两个用户了。

- 第二个作用是记录历史。
- - 假设 http://a.com 是一个购物网站，当 A 在上面将商品 A1 、A2 加入购物车时，JS 可以改写 Cookie，改为「uid=1; cart=A1,A2」，表示购物车里有 A1 和 A2 两样商品了。
- - 这样一来，当用户关闭网页，过三天再打开网页的时候，依然可以看到 A1、A2 躺在购物车里，因为浏览器并不会无缘无故地删除这个 Cookie。
- - - 借此，就达到里记录用户操作历史的目的了。
## cookie 基础 （http rfc）
- HTTP请求是无状态的，每一次的请求对于服务器和浏览器来说都是一样的，通过Cookie维持状态
- 浏览器看到服务器在ResponseHeader响应中返回set-cookie的时候，就会在之后的每一次请求都把cookie的内容放在RequestHeader里面 
- 用户信息保存在cookie，所以 cookie的内容包含用户信息，每一次包含cookie的请求都表示是该用户的行为
- cookie和浏览器域名是相绑定的，cookie会规定在相应的域名`/path`请求才能够携带cookie 
- cookie的httpOnly 表示cookie只有能够被浏览器发送 