# 概念

![image-20230201171051766](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230201171051766.png)

## 协议介绍

Http 协议属于 TCP/IP 协议族，是网页中传递信息的主要协议。

DNS 服务：位于应用层，提供域名到 IP 之间的解析服务

### IP 协议（不是 IP 地址）

作用：将数据包传输给对方

IP 地址：节点在网络上被分配的地址

MAC 地址：网卡所属的固定地址

### TCP 协议

作用：提供可靠的字节流服务，能够确认数据是否发送到了对方

为了确认数据是否准确的发送到了对方，TCP 协议采用了三次握手策略

![image-20230201171624268](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230201171624268.png)

## URL 和 URI

URL 就是统一资源定位符，例如 `http://www.baidu.com/news`

URI 是统一资源标识符，就是除开请求协议和域名之后的部分，例如 `/news`

# 简单 HTTP 协议

## 报文

在一条通信线路上必定有一端是客户端，另一端则是服务器端。并且请求从客户端发出，服务端回复响应。

![image-20230202170544078](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230202170544078.png)

请求报文由请求方法、URL、协议版本、可选择的首部字段和内容实体构成。如下

![image-20230202171656314](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230202171656314.png)

响应报文的构成：

![image-20230202171812613](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230202171812613.png)

HTTP 不对请求和响应的通讯状态进行保存，是`无状态协议`。这是为了 更快地处理大量事务，确保协议的可伸缩性。

随着技术的发展，网站上需要保存用户的某些状态信息，于是引入了 `Cookie` 技术

## 方法

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230202173142898.png" alt="image-20230202173142898"  />

## 持久连接

HTTP 协议的初始版本中，每进行一次 HTTP 通信就要建立一次 TCP 连接，用完就断开，增加了通信量的开销。

`HTTP/1.1` 和一部分的 `HTTP/1.0` 想出了 持久连接（HTTP Persistent Connections，也称为 HTTP keep-alive 或 HTTP connection reuse）的方法。持久连接的特点是，只要任意一端 没有明确提出断开连接，则保持 TCP 连接状态。

在 `HTTP/1.1` 中，所有连接默认都是持久连接，`HTTP/1.0` 没有标准化，但是有一部分服务器通过非标准手段实现了持久连接。

## 管线化

从前发送请求后需等待并收到响应，才能发送下一个请求。管线化技术出现后，不用等待响应亦可直接发送下一个请求。

![image-20230202173928985](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230202173928985.png)

## 使用 Cookie 的状态管理

Cookie 会根据从服务器端发送的响应报文内的一个叫做 Set-Cookie 的 首部字段信息，通知客户端保存 Cookie。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 Cookie 值后发送出去。

## HTTP 报文中的 HTTP 信息

HTTP 报文大致可分为报文首部和报文主体两块，报文主体不是必须的。

一般有 4 种首部，分别是：通用首部、请求首部、响应首部和实体首部。

![image-20230206164017697](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230206164017697.png)

请求和响应报文示例：

![image-20230206164154626](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230206164154626.png)

### 编码提升传输效率

HTTP 在传输数据时可以按照数据原貌直接传输，也可以在传输过程中通过编码提升传输速率。

通过在传输时编码，能有效地处理大量的访问请求。但是编码的操作需要计算机来完成，因此会消耗更多的 CPU 等资源。

内容编码指明应用在实体内容上的编码格式，并保持实体信息原样压缩。内容编码后的实体由客户端接收并负责解码。











