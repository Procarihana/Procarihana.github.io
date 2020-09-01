---
title: "计算机网络TCP and Socket"
date: 2020-08-30T11:39:13+08:00
draft: false
toc: false
images:
tags: [Socket,TCP]
---

## 没有100%可靠的通信协议
- 红军/蓝军
- 计算机网络：虚电路分组，A服务器把报文给离目标IP最近的力所能及的设备（过程为HOP），这个设备再把报文发给下一个设备，如果中间某个最佳的设备被毁，就选择另一个设备绕过，直到到达目标IP（快递发货）
- 

## 网络分层模型
- TCP/IP模型（应用层、传输层【四层交换机、四层路由器】、网络层【路由器，三层交换机】）
- TCP Transmisson Control ProTocal 传输控制协议  
- - TCP的握手与断开
- - - 三次握手建立连接
> - `SYN SENT seq = x` 发送请求建立连接的请求 -> 获得`SYN seq=y,ACK x+1` （同意建立请求）
> - `ACK =y+1` 表明收到同意建立连接 ==> 连接建立，
- - - 四次挥手断开连接
> - `FIN seq = x+2 ACK = y+1`发送断链接的请求 -> 获得`ACK x+3` 表示收到，又获得 `FIN seq= y+1` 表示断开链接
> - `ACK = y+2` 表示收到同意断开链接
- - - 如果中间有信息丢掉没有成功收到，会通过请求超时来知道链接断开成功
- TCP 协议无法传递数据包，只能把所有数据包拆、黏包
- - TCP 没有规定包的界限，需要自己设计协议、分隔符、结束符来设计包的边界
- TCP 中的数据包含TCP 包首部和数据两个部分，而IP中的数据包含前两个部分，在加上IP包首部
- TCP 只有两个字节的端口（65536），端口数量有限制（同时使用），而每个端口访问的对象是不限制
- - TCP数据里面有确认号码，如果接受的没有收到，就需要重传。校验和可以检验数据是否有被串改，如果有则向发送的一方放再次发送请求
的
- 以太网则在IP数据的基础杀昂添加以太网包首部
- TCP特点
- - 面向连接 
- - 点对点 需要有目标端口
- - 可靠交付 校验和、序列号码、确认号码 保证数据正确
- - 面向字节流
- UDP 协议（用于视频）
- - 无连接的 无法保证数据是否到达
- - 尽最大可能交付
- - 面向报文的
## Scoket
- TCP包含客户端和服务器双方的ip和port。这四个元素是四元组（client的src源ip、port和dest目标ip。port），也就是socket/socket的链接。
- - ip --> 唯一确定一台计算机，port --> 端口可以唯一确定监听端口的一个程序
- socket 只要制定目标ip和port 就可以发送信息，自身ip会自动提供，如果有指定的port就会使用，如果没有就会自动挑选一个空闲的端口
- - 查询网站ip地址： $ nslookup + 地址
```java
public class SocketTest {

    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("182.61.200.7",443);
        socket.getOutputStream().write('a');
    }
}
```
- Socket 里传输速Http 协议
- - 抵达服务器地第一个字节一定时是`G`之后是`E`、`T`、`0x20`
- - 按照HTTP协议格式的规定，给服务器返回字节流，就能够手写出HTTP服务器

- - 请求行：

|请求方法|`|`空格|`|`URL方法|`|`空格|`|`协议版本|`|`回车符（\r）|`|`换行符（\n）|
|---|---|---|---|---|---|---|---|---|---|---|
- - 请求头部：

|头部字段名|`|`：|`|`value|`|`\r|`|`\n|
|---|---|---|---|---|---|---|---|---|---|---|
|……|
头部字段名|`|`：|`|`value|`|`\r|`|`\n|
\r|`|`\n|
- - 请求数据
- 手写HTTP协议
- - 本地环回网络接口：127.0.0.1。 能够通过这个接口访问自己的机器
- - `socker.accept()`执行后要是没有设置返回或者没有下一步的操作，就会一直停在那里阻塞，返回的是一个socket。
```java
public class SocketTest {

    public static void main(String[] args) throws IOException {
        int port = 8080;
        ServerSocket serverSocket = new ServerSocket();
         serverSocket.bind(new InetSocketAddress("127.0.0.1",port));
        System.out.print(serverSocket.accept());
    }
}
serverSocket.accept()：Socket[addr=/127.0.0.1,port=54777,localport=8080]

```
```java
public class SocketTest {

    public static void main(String[] args) throws IOException {
        int port = 8080;
        ServerSocket serverSocket = new ServerSocket();
        serverSocket.bind(new InetSocketAddress("127.0.0.1", port));
        Socket socket = serverSocket.accept();
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
        String line = bufferedReader.readLine();
        System.out.println(line);
        //HTTP response header
        socket.getOutputStream().write("HTTP/1.1 200OK\r\n".getBytes());
        //HTTP response header/body 分隔符
        socket.getOutputStream().write("\r\n".getBytes());
        //HTTP response body
        socket.getOutputStream().write("Hello!".getBytes());

        //清理缓冲区，避免堵在缓冲区没有执行
        socket.getOutputStream().flush();
    }
}
```



