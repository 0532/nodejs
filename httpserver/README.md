# http.Server
此模块会触发以下事件


**Event: 'request'**
```
function (request, response) { }```

request 是http.ServerRequest 的一个实例，而response 则是http.ServerResponse 的一个实例

**Event: 'connection'**


```
function (stream) { }```

当一个新的TCP stream 建立后发出此消息。stream 是一个net.Stream 的对象，通常用户不会访问/使用这个事件。
参数stream 也可以在request.connection 中访问到.


**Event: 'close'**
```
function (errno) { }```

当服务器关闭的时候触发此事件。


**Event: 'request'**
```
function (request, response) {}```

每个请求发生的时候均会被触发。请记住，每个连接可能会有多个请求(在keep-alive 连接情况下)


**Event: 'upgrade'**
```
function (request, socket, head)```

每当一个客户端请求一个http upgrade 时候发出此消息。如果这个事件没有监听，那么请求upgrade 的客户端对
应的连接将被关闭。

1.参数“request”代表一个http 请求，和'request'事件的参数意义相同。

2.socket 是在服务器与客户端之间连接用的网络socket

3.head 是Buffer 的一个实例,是upgraded stream(升级版stream....应当就是http upgrade)所发出的第一个包，这个参数可以为空。

当此事件被触发后，该请求所使用的socket 并不会有一个数据事件的监听者,这意味着你如果需要处理通过这个
SOCKET 发送到服务器端的数据的话则需要自己绑定数据事件监听器


**Event: 'clientError'**
```
function (exception) {}```

如果一个客户端连接的'error'事件被触发，此函数将被执行。


**http.createServer(requestListener)**


返回一个新的web server 对象。


requestListener 是一个会去自动监听'request'事件的函数。


**server.listen(port, [hostname], [callback])**


在指定端口和主机名上接受连接。如果hostname 没有写,这个服务器将直接在此机器的所有IPV4地址上接受连
接(INADDR_ANY).


如果要在UNIX SOCKET 上监听的话，则需要提供一个文件名来替换端口和主机名.
这个方法是一个异步的方法，作为最后一个参数的回调方法将在服务器已经在此端口上绑定好后被调用.


**server.listen(path, [callback])**


建立一个UNIX SOCKET 服务器并在指定路径监听。
这个方法是一个异步的方法，作为最后一个参数的回调方法将在服务器已经在此端口上绑定好后被调用。


**server.setSecure(credentials)**


允许此服务器支持HTTS，配合crypto 模块credentials 指定私钥以及此服务器的证书，并且也可选择数字中心认
证的证书作为客户端的认证（方式）。


如果authentication 中有一或多个数字认证中心证书，则服务器将请求客户端发出一个客户端证书作为HTTPS
握手的一部分。想要验证、访问证书合法性及内容则可以通过服务器的request.connection 的verifyPeer() 以及
getPeerCertificate() 来实现。


**server.close()**


使此服务器停止接受任何新连接。


**server.maxConnections**


设置此属性使服务器的连接数高于此数值时拒绝连接。


**server.connections**


代表当前服务器的连接数。
