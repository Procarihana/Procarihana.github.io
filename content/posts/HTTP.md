---
title: "HTTP"
date: 2020-10-21T09:45:43+08:00
draft: true
toc: false
images:
tags: ["",""]
---

## HTTP metood


第一个单词表明请求的方法
##### GET
请求 拿一个网页
请求的数据全部都放在HTTP request header中

Host 网络主机名的别称

DNS 把网络名编程IP地址
  通过sudo 来改变本地 网络的DNS对应的IP地址
所有访问都要通过IP地址才能访问

local host == 127.0.1

反爬虫

1. status 401/403

2. 在返回的数据中投毒
##### POST
请求登陆

发送一个header，真正的数据保存在body里面
##### PUT 
##### DELETE
## HTTP status

状态码
##### 2xx 

200OK 表示请求成功 
##### 3xx

重定向 永久移动 并且会发送一个新的地址给你
##### 4xx

客户端的错误，无法完成请求

用GET方法发送请求登陆获得405，客户端用错误的方法发出请求
##### 5xx

服务器有问题
##### Joke:6xx
## HTTP 请求header
##### 重要的header
- Accept*


- - Accept-Language


- Cookie
- User-Agent
显示发出请求的客户端

不区分大小写

可以通过添加来伪装成一个浏览器，从而绕过防爬虫的阻碍
- Referer

显示从那个页面跳转过来的

可以用于防盗

## HTTP响应header
响应是根据请求完成的

##### 重要的header
- Content-type
Accept 发出什么请求，Content-tupe 就会返回什么类型的数据
二者协同工作，http 就能够运行

服务器告诉浏览器当前的响应应该如何被解析
虽然是字节流，但是如果字节流提示的是图片，浏览器就会解析成图片
- Set-Cookie
第一次执行登陆请求的时候，服务器返回的时候就会返回一个Set Cookie 后面一串随机的字符串

之后所有发出去的请求就会带有这个请求

这一串字符串就代表一个固定的用户，从而识别请求的用户

浏览器是自动的，但是只对当前域名有效


Cookie 和Set-Cookie是安全的基石，但是是有有效期的
## HTTP body
GET是没有body的

encodeURIComponent  把除特定字符以外的字符转换
##### HTTP request body
- 表单
- k-v对
##### HTTP response body
- JSON
- HTML/XML
- 二进制（图片/下载文件）
## HTTP协议是无状态的

HTTP 请求彼此之间是独立的
因为访问的服务器可能是不同的服务器，之间不一定存在沟通交流
HTTP不保证状态

## 模拟浏览器登陆网页
    public class Crawler {
    static String cookie;

    public static String loginAndGetResponse(String username, String password) throws IOException {
        String request;

        CloseableHttpClient httpclient = HttpClients.createDefault();
        HttpPost httpPost = new HttpPost("http://47.91.156.35:8000/auth/login");


        Map<String, String> map = new HashMap<>();
        map.put("username", username);
        map.put("password", password);
        String json = JSON.toJSONString(map);
        StringEntity stringEntity = new StringEntity(json, "UTF-8");
        httpPost.setEntity(stringEntity);


        httpPost.addHeader("Content-Type", "application/json");
        httpPost.addHeader("User-Agent", "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.11 (KHTML, like Gecko) Chrome/23.0.1271.64 Safari/537.11");

        CloseableHttpResponse response = httpclient.execute(httpPost);

        try {
            System.out.println(response.getStatusLine());
            HttpEntity httpEntity = response.getEntity();

            InputStream inputStream = httpEntity.getContent();
            request = IOUtils.toString(inputStream, "UTF-8");
            String[] headers = response.getFirstHeader("Set-Cookie").getValue().split(";");
            cookie = headers[0];

        } finally {
            httpclient.close();
        }
        return request;
    }

    public static String HttpGetQuest() throws IOException {
        String result;
        CloseableHttpClient httpclientQuest = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet("http://47.91.156.35:8000/auth");
        httpGet.setHeader("Cookie", cookie);
        CloseableHttpResponse response1 = httpclientQuest.execute(httpGet);
        try {
            System.out.println(response1.getStatusLine());
            HttpEntity entity1 = response1.getEntity();

            result=IOUtils.toString(entity1.getContent(), "UTF-8");
            System.out.println(result);
            EntityUtils.consume(entity1);
        } finally {
            response1.close();
        }
        return result;
      }
    }


## 补充
https://blog.csdn.net/yyz_suhua/article/details/74722193

https://blog.csdn.net/justry_deng/article/details/81042379

http://hc.apache.org/httpcomponents-client-ga/quickstart.html