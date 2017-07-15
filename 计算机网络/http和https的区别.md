---
title: http和https的区别
date: 2017-05-13 21:11:42
categories: 计算机网络
tags: [http,https]
---

http协议是未加密的传输，即采用明文的方式发送内容，如果攻击者截取了web浏览器和网站服务器之间的传输报文，就很容易直接读取其中的信息。https就是在http的基础之上加入了SSL协议，SSL用于对http协议传输的数据进行加密。

- https协议需要到ca申请证书，一般需要一定的费用
- http是超文本传输协议，信息是明文传输。https则是具有安全性的ssl加密协议
- http和HTTPS使用不同的连接方式，用的端口也不同，前者是80端口，后者是443端口
- http的连接是简单无状态的，HTTPS协议是由ssl+http协议构成的可进行加密传输、身份认证的网络协议，比http协议安全。

<!--more-->

#### HTTP1.0 与HTTP1.1的区别

- 引入持久化连接，即TCP连接默认不关闭，可以被多个请求复用，不用声明`Connection: keep-alive`。
- 引入管道机制，即在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。

#### Http状态码：

- 1xx：指示信息，表示请求已经接收，继续处理
- 2xx：成功，表示请求已经被成功接收、理解、接受
- 3xx：重定向，表示完成请求必须进行更进一步的操作
- 4xx：客户端错误，请求有语法错误或者请求无法实现
- 5xx：服务器端错误，服务器未能实现合法的请求

**常见的状态码：**

- 200：OK——客户端请求成功
- 400：Bad request——客户端请求有语法错误，不能被服务器解析
- 401：unauthorized——请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
- 403：Forbidden——服务器收到请求，但是拒绝提供服务
- 404：Not found——请求资源不存在  如URL输入错误
- 500：Internal Server Error——服务器发生了不可预料的错误
- 503：Server Unavailable——服务器当前不能处理客户端的请求，一段时间后可能恢复正常



#### Http Request请求头常见字段：

- Http请求方式

  ​

- Host：请求的web服务器域名地址

- User-Agent：Http客户端浏览器类型的详细信息

- Accept：指定客户端能够接收的内容类型 text/xml、text/html

- Accept-Language：指定客户端浏览器用来显示返回信息所优先选择的语言

- Accept-Encoding：指定客户端浏览器可以支持服务器返回内容的压缩编码类型，表示客户端浏览器所能支持的返回压缩格式

- Accept-CharSet：浏览器可以接受的字符编码集

- Content-type：此请求提交的内容类型，一般只有post提交时才需要设置该属性

- Connection：表示是否需要持久连接，如果值是keep-Alive或者协议版本是HTTP1.1，就会进行持久连接

- Cookie：http请求发送时，会把保存在该请求域名下的所有cookie值一起发送给web服务器

- Date：请求发送的日期时间

#### Http Response响应头字段：

- Cache-Control：缓存机制,no-cache,private,public,
- Connection:是否要保持持久连接
- Content-Encoding：返回来数据的压缩格式
- Content-Language：相应体的语言
- Content-type：返回资源文件的类型
- Date：服务器发送资源时的服务器时间
- Expiers:告诉客户端在这个时间前可以直接访问缓冲副本，相应过期时间
- Last-Modifed:请求资源的最后修改时间