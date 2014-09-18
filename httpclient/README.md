# http.Client

使用服务器地址作为参数来构造一个HTTP client，其返回的句柄可用来发出一个或者多个请求。根据连接的服
务器不同，这个客户端可以使用管道处理机制来处理请求或者每个请求重新构建stream。当前的实现方式并没
有用管道处理机制处理请求.


```
Example of connecting to google.com:
var http = require('http');
var google = http.createClient(80, 'www.google.com');
var request = google.request('GET', '/',
{'host': 'www.google.com'});
request.end();
request.on('response', function (response) {
console.log('STATUS: ' + response.statusCode);
console.log('HEADERS: ' + JSON.stringify(response.headers));
response.setEncoding('utf8');
response.on('data', function (chunk) {
console.log('BODY: ' + chunk);
});
});
```
如下消息头应当注意：


Node 并不会添加'Host'，但是这个属性对于一个网站来说通常是必须的。



发送'Connection: keep-alive'将告知Node 和服务器之间的连接应当是持久连接，直到下一次请求才断开。
发送'Content-length'标记将禁用默认的消息体编码。


**Event: 'upgrade'**
```
function (request, socket, head)```

当服务器响应upgrade 请求时触发此事件，如果这个消息没有被监听，客户端接收到一个upgrade 头的话会导致
这个连接被关闭。


可以查看http.Server 关于upgrade 事件的解释来了解更多内容。


**http.createClient(port, host='localhost', secure=false, [credentials])**


构造一个新的HTTP 客户端.port 和host 指明了将要连接的目标。在发出请求之前不会建立流(establishe a stream)。
secure 是一个可选的布尔值，用来表示是否启用HTTPS。credentials 是一个来自于crypto 模块的可选参数，
credentials 中可以包含client 的私钥，证书以及一个可信任的数字认证中心的证书列表.
如果连接使用了secure 但是没有把数字认证中心证书传给credentials，那么NODEJS 将缺省使用公开的可信任
数字认证中心整数列表,就比如http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt


**client.request(method='GET', path, [request_headers])**


发出一个请求，在必须时建立一个流。该函数返回一个http.ClientRequest 对象。method 是可选项，缺省会以
GET 方式发出请求


request_headers 是可选项。请求头的额外部分一般由NODE 内部实现。该函数返回一个ClientRequest 对象
如果你就想要发送一个信息体，记得要在头信息里包含Content-Length 项。如果你想要将BODY 通过流的方式
传输发送，或许需要设置Transfer-Encoding: chunked.


译注:大多数的站点相应用户请求时发送的HTTP Headers 中包含Content-Length 头.此头信息定义在HTTP1.0协
议RFC 1945 10.4章节中.该信息是用来告知用户代理,通常意义上就是浏览器,服务端发送的文档内容长度.浏览
器接受到此信息后,接收完Content-Length 中定义的长度字节后开始解析页面.如果服务端有部分数据延迟发送,那么浏览器就会白屏.这样导致比较糟糕的用户体验. 解决方法在HTTP1.1协议.RFC2616中14.41章节中定义的
Transfer-Encoding:chunked 的头信息.chunked 编码定义在3.6.1中.根据此定义浏览器不需要等到内容字节全部下
载完成,只要接收到一个chunked 块就可解析页面.并且可以下载html 中定义的页面内容,包括js,css,image 等.


注意：这个请求并不完全。这个方法仅仅发送了头和请求。需要发送一个request.end()来真正的完成当前这个请
求并且接收回应。（这听起来有点绕,但是这正好就提供了用户通过使用request.write()方法来通过流方式发送
body 到服务器端的机会）


**client.verifyPeer()**


返回true/false 并在上下文附带服务器定义的或者缺省数字认证中心的有效证书列表。


**client.getPeerCertificate()**


返回用JSON 结构详尽表述的服务器方证书，其中包含证书的‘主题’，‘发布者’，'有效来源','有效目标'（'subject',
'issuer', 'valid_from' and 'valid_to'）。
