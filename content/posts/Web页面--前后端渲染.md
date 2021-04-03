---
title: "Web页面  前后端渲染"
date: 2020-09-07T14:59:13+08:00
draft: false
toc: false
images:
tags: [Web,渲染]
---
- 前端渲染：前端发送静态的html，浏览器下载html里面的JS、CSS文件，等待下载完后就加载并初始化JS代码，可运行后就想后端请求数据，等待后端完成数据返回。客户端从无到完整，把数据渲染为响应页面
- 后端渲染：服务器端请求数据（内网请求快），服务器初始渲染（服务端性能较快），服务端返回已经有正确内容的HTML，客户端请求JS/CSS文件，等待下载完成且加载并初始化完成
- 前端渲染：前端发送静态的html数据
- 后端渲染：后端发送htlm数据
## 渲染模板
- index.html -> 静态模板  
```json
<!DOCTYPE heml>
</head>
<body>

<h2>商品排行榜</h2>

<table>
  <tr>
    <th>排名</th>
    <th>商品名</th>
    <th>成交金额</th>
  </tr>
  <tr>
    <td>1</td>
    <td>西瓜</td>
    <td>400</td>
  </tr>
  <tr>
    <td>2</td>
    <td>香蕉</td>
    <td>200</td>
  </tr>
  <tr>
    <td>3</td>
    <td>...</td>
    <td>...</td>
  </tr>
</table>

</body>
</html>
```
- 动态模板引擎 -> spring-freemarker
- - 模板引擎就是能够然数据替换
- maven
```java
<!-- spring-boot-starter-freemarker -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```
- 文件结构 (View)
- resources/templaes/~.ftl -> 存放动态模板
- - 其中list 为结果集 name->items
```json
<table>
    <tr>
        <th>排名</th>
        <th>商品名</th>
        <th>成交金额</th>
    </tr>
   <#list items as item>
    <tr>
        <td>${item.qualifying}</td>
        <td>${item.goodsName}</td>
        <td>${item.totalPrice}</td>
    </tr>
    </#list>
</table>
```
#### 如果你使用的是最新版的Spring Boot （2.2+），默认的Freemarker扩展名变成了ftlh！如果仍然使用ftl会报错404！请千万注意！
### 也可以通过在Spring配置里面设置完成
```
# freemarker配置
spring.freemarker.template-loader-path=classpath:/templates
spring.freemarker.suffix=.ftl
```

- Controller 返回模板进行渲染 
- HashMap 存放返回的数据
- put（”模板引擎留下的对应的位置命名“,数据） 
- 返回ModelAndView(viewname,hashmap )
- - viewname 就是`~`(~.ftl)

```java
@GetMapping(path = "/goodsRank", produces = "application/json")
    public ModelAndView getGoodsRank() {
        List<RankItem> items = goodsRankManager.getGoodsRank();
        HashMap<String, Object> model = new HashMap<>();
        model.put("items", items);
        return new ModelAndView("Rank", model);
    }
}
```
## 使用JS和JSON异步请求进行前端渲染
- 在resouces/static/放`.html`
- - freemaker 里面的`.ftl`文件改名然后放进static 就可
- 在里面通过