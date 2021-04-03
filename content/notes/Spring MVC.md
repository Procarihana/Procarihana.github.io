---
title: "Spring MVC"
date: 2020-09-17T23:41:33+08:00
draft: true
tags: ["MVC","Spring MVC"]
---
## Spring MVC
-  模型model(javabean),  视图view(jsp/img)   控制器Controller(Action/servlet)  
- C 存在的目的就是为了保证M和V的一致性
   当M发生改变时,C可以把M中的新内容更新到V中.
   >- 使用MVC模式的好处是，Controller专注于业务处理，它的处理结果就是Model。Model可以是一个JavaBean，也可以是一个包含多个对象的Map，Controller只负责把Model传递给View，View只负责把Model给“渲染”出来，这样，三者职责明确，且开发更简单，因为开发Controller时无需关注页面，开发View时无需关心如何创建Model。


- **SpringMVC是Spring框架内置的MVC的实现，一个Spring内置的MVC框架**  
MVC框架，它解决WEB开发中常见的问题(参数接收、文件上传、表单验证、国际化、等等)，而且使用简单，与Spring无缝集成。   
支持 RESTful风格的 URL 请求 。  
采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性。
- 为了解决页面代码和后台代码的分离

