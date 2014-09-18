# http.ClientResponse
这个对象在使用http.Client 发起请求时被创建，它会以参数的形式传递给request 对象'response'事件的响应函数。
'response'实现了可读流的接口。


**Event: 'data'**
```
function (chunk) {}```

当接收到消息体一部分的时候触发。


例如：此方法有一个参数，是消息体的一个块，这个块的内容已经被解码成一个字符串。信息体的编码通过
`response.setBodyEncoding()`来设置。


**Event: 'end'**
```
function () {}```

该事件对于每个受到的消息会触发一次。它没有参数，在触发过这个事件后将不会再触发其他事件将了。



**response.statusCode**


一个三位数字所表示的HTTP 回应状态，比如404。


**response.httpVersion**


所连接到的服务器所使用HTTP 协议版本。大致上是'1.1'或者'1.0'。response.httpVersionMajor 则表示第一个数字
response.httpVersionMinor 表示第二个。


**response.headers**


http 信息头对象。


**response.setEncoding(encoding='null')**


设置回应信息体的编码，'utf8'、'ascii'或者'binary'。默认值为空，着表示'data'事件将会使用一个Buffer 对象作为
参数。


**response.pause()**


暂停response 触发事件。通常在控制一个下载动作时候使用。


**response.resume()**


恢复一个暂停的请求。


**response.client**


保存response 所属的http.Client 的引用。
