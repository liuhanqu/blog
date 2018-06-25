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

# 参考资料

1.  [SSL/TLS in Detail](<https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc785811(v=ws.10)>)
2.  [史上最完整的 Tengine HTTPS 原理解析、实践与调试](https://yq.aliyun.com/articles/597667?utm_content=m_51050)
3.  [HTTPS 权威指南]
