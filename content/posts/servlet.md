---
title: "Servlet"
date: 2020-10-04T14:18:34+08:00
draft: true
toc: false
images:
tags: ["servlet",""]
---
 Socket 从客户端到服务器的四元组，能够读写数据（IntOutputStream）
 Servlet 不需要手动通过socket写符合http协议的字节流给服务器，一套规范，但本质上是Java 的接口，避免手工处理http的内容，是web应用的基石
- Java Servlet api maven
- -  HttpServerRequest 能够完成getCookie()、getConrectTtpe()、header等等，能够读取能够生成，状态码也可以
- 全是接口，不需要具体实现
---
- Socket 把字节流封装成HttpServletRequest对象 和 HtppServletResponse 对象，
- ServletRequest 可以直接强制类型转换成HttpServletRequest 
- request.getParameter("") 设置Servlet对象
- 设置Json = `"{\"requestParameter\"+":"+ }"`
- response.setContentType()
- response.getOutputStream().write(Json.getByte())
- 把outputStream flush（） 
---
- Servlet 容器 为Servlet 提供运行
- - Servlet 不能够独立运行，必须依赖容器
- -  bin 目录下面存放着可运行的程序
- - - bat 是windows sh 是Mac
- Tomcat 的可执行程序同样是放在bin目录里面 核心为 catalina 
- - 运行tomcat 就需要把 运行的Servlet 存放在 catalina_base/webapps/.下面，当然放进去的要是字节码，打包在jar里面  
- - Tomcat  里面的web.xml 能够给Tomcat只是运行Servlet,修改或者增添Servlet-Name就能够让Tomcat识别到要运行的Servlet 
- - 把 字节码打成jar包放在bin下面，`bin/catalina.sh start  `

---
查看Tomcat 日志
- 打开到apache-tomcat-x.x.x目录，`vi logs/catallina.0000-00-00`日志
- - -
- jsp 后端渲染，把模板渲染成html 有一堆的模板文件。本质就是把java代码填充到HTML 里面
---
webSocket
---
访问Servlet 前 需要进行访问权限校验进行拦截，就是Filter接口 完成
-doFilter 要在请求 或者response 进行拦截， FilterChain 能够把通过拦截的的请求发送给下一个拦截，或者不发送
---
Spring 里面没有web.xml(把配置加载到tomcat)，而spring和tomcat是一个整体，所以tomcat能够识别到spring 里面的sevlet，不需要额外的配置
---
listener 接口
用于启动Spring
---

Servlet 的 filter 负责请求的拦截
Aop  负责最后的Servlet 拦截
Intecceptor 负责SpringMvc切面的拦截
   
