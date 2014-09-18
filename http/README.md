# HTTP
如果要使用HTTP 的server 以及client 模块则必须使用require('http')加载http 模块


NODE 中的HTTP 接口被设计成为支持HTTP 协议的很多特性，这些特性通常那难以掌控，特别是large,possiblechunk-encoded(块编码),messages。这个接口特意不缓冲整个请求(request)或者响应(responses)使用户可以使用流
的形式操作数据。


以下是用对象的形式表示的HTTP 信息头
```
{ 'content-length': '123' , 'content-type': 'text/plain' , 'stream': 'keep-alive' , 'accept': '*/*' }```

所有key 都是小写，数值不能被修改


为了支持尽可能多的HTTP 应用，NODE 的HTTP API 非常底层。其只处理到流(stream)相关的操作以及信息解
析。API 将信息解析成为信息头和信息体，但并不解析实际的信息头和信息体的具体内容。


如果在基础平台上的OpenSSL 是可用的则HTTPS 也能够被支持
