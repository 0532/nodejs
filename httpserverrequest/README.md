# http.ServerRequest
这个对象通常由HTTP SERVER 建立而非用户手动建立，并且会作为传递给'request'事件监听器第一个参数
此对象的可以触发以下事件：


**Event: 'data'**
```
function (chunk) { }```

当接收到信息体中的一部分时候会发出data 事件。
例如:代表消息体的数据块将作为唯一的参数传递给回调函数。这个时候数据已经按照传输编码进行了解码（不
是字符集编码）。消息体本身是一个字符串，可以使用request.setBodyEncoding()方法设定消息体的编码。


**Event: 'end'**
```
function () { }```

每次完全接收完信息后都会触发一次。没有参数，当这个事件发出后，将不会再触发其他事件。


**request.method**


request.method 是一个只读字符串。例如'GET','DELETE'


**request.url**


代表所请求的URL 字符串.他仅包括实际的HTTP 请求中的URL 地址。如果这个请求是
```
GET /status?name=ryan HTTP/1.1\r\n
Accept: text/plain\r\n
\r\n```

则request.url 应当是


    '/status?name=ryan'


如果你想要解析这个URL
中的各个部分，你应当使用require('url').parse(request.url).
Example:


```
node> require('url').parse('/status?name=ryan')
{ href: '/status?name=ryan'
, search: '?name=ryan'
, query: 'name=ryan'
, pathname: '/status'
}
```
如果你想从查询字符串中提出这些参数，你可以使用require('querystring').parse 方法,或者传一个true 作为第二个
参数给require('url').parse 方法。
Example:
```
node> require('url').parse('/status?name=ryan', true)
{ href: '/status?name=ryan'
, search: '?name=ryan'
, query: { name: 'ryan' }
, pathname: '/status'
}```

**request.headers**


只读


**request.httpVersion**


这是HTTP 协议版本（ 字符串形式) ， 只读。例如'1.1','1.0' 。request.httpVersionMajor 是第一个数字，
request.httpVersionMinor 是第二个数字。


**request.setEncoding(encoding='null')**


设置此请求的包体的字集编码,'utf8'或者'binary'。缺省值是null，这表示'data'事件的参数将会是一个Buffer 对象。


**request.pause()**


暂停此request 触发事件.对于控制上传非常有用。


**request.resume()**


恢复一个暂停的request。


**request.connection**


request.connection 是一个代表当前连接的net.Stream 对象。
对于HTTPS，使用request.connection.verifyPeer() 和request.connection.getPeerCertificate()来获得客户端（浏览
器）的认证详情。
