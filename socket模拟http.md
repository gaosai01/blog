## Socket模拟Http请求

> 作者：Mr-GaoSai
> 
> 时间：2017/8/12

socket是对TCP/IP协议(传输层)的封装和应用（程序员层面上）

Http协议(应用层协议)则是由Socket模拟实现。

***这篇文章主要讲解Http协议方面的知识还有TCP/IP协议的三次握手和四次挥手***

一个Http请求先要通过三次握手和服务器**建立连接**，然后发送特定格式的请求头到服务器端，然后服务器端返回响应头，然后客户端或者服务端进行四次挥手**断开连接**。

### 请求头
```
GET / HTTP/1.1
Host: www.zhihu.com
Connection: close
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36
Accept: */*
```

请求头的Source。



### 响应头
```
HTTP/1.1 301 Moved Permanently
Date: Sat, 12 Aug 2017 09:49:48 GMT
Content-Type: text/html
Content-Length: 178
Connection: close
Set-Cookie: aliyungf_tc=AQAAADiSQErS+AUA3pqBG7uYN7qYkzRC; Path=/; HttpOnly
Location: https://www.zhihu.com/question/63800535
X-Req-ID: 128BBB4598ECF37
Server: ZWS
Vary: Accept-Encoding

<html>
</html>
```

响应头的Source

响应码 301重定向 Location


### 三次握手

位码即***tcp标志位***,有6种标示:

SYN(synchronous建立联机)

ACK(acknowledgement 确认)

PSH(push传送)

FIN(finish结束)

RST(reset重置)

URG(urgent紧急)

### 四次挥手

![](tcp34.png)