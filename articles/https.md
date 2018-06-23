# HTTPS 的定义

HTTPS（HyperText Transfer Protocol over Secure Socket Layer)，在 HTTP 的后面加多了一个 S，可以理解在 HTTP 的基础上了多了一个 secure，即安全的 HTTP。HTTP 是应用层协议，在运行在 TCP 层之上，HTTPS 就是在 TCP 和 HTTP 层加了一层 SSL 层，向上提供加密和解密的服务。

# HTTPS 为什么安全

通过非对称加密来协商对称加密的密钥，握手成功之后便可使用对称加密来做加密通信，对于 RSA 来说，客户端是用 RSA 的公钥把预主密钥加密后传给服务器，服务器再用私钥来解密，双方再通过相同的算法来生成会话密钥，之后的应用层数据就可以通过会话密钥来加密通信。

# HTTPS 握手过程

1.  客户端请求 Client Hello

```
  ClientVersion 3,1  (支持的协议版本)
  ClientRandom[32]  (客户端随机数，用于之后生成秘钥)
  SessionID: None (new session)  (SessionID)
  Suggested Cipher Suites: (支持的加密方法)
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_RSA_WITH_DES_CBC_SHA
  Suggested Compression Algorithm: NONE (支持的压缩方法)
```

2.  服务端响应

- Server Hello，消息主要包含以下内容

```
  Version 3,1  （通信协议版本）
  ServerRandom[32]  （服务端随机数，用于之后生成秘钥）
  SessionID: (SessionID)bd608869f0c629767ea7e3ebf7a63bdcffb0ef58b1b941e6b0c044acb6820a77
  Use Cipher Suite: （加密方法）
  TLS_RSA_WITH_3DES_EDE_CBC_SHA
  Compression Algorithm: NONE （压缩方法）
```

- Server Certificate， 服务器证书
- Server Hello Done

3.  客户端响应

- Client Key Exchange （随机数，服务器公钥加密）
- Change Cipher Spec （编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送）
- Client Finished （客户端握手结束）

4.  服务端响应

- Change Cipher Spec Message （编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送）
- Server Finished Message （消息内容为前面发送的所有内容的 hash 值，用来客户端校验。）

完整的握手过程如下图

![](/images/https-handshake.jpg)

# 参考链接

[SSL/TLS in Detail](<https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc785811(v=ws.10)>)
[史上最完整的 Tengine HTTPS 原理解析、实践与调试](https://yq.aliyun.com/articles/597667?utm_content=m_51050)
[HTTPS 权威指南]
