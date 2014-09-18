# http.ServerResponse
这个对象一般由HTTP 服务器建立而非用户自己手动建立。它作为'request'事件的第二个参数，这是一个可写流。


**response.writeHead(statusCode, [reasonPhrase], [headers])**


这个方法的是用来发送一个响应报文头给本次的请求方，第一个参数状态码是由一个3位数字所构成的HTTP 状
态，比如404之类的。最后一个参数headers 是响应头具体内容.也可以使用一个方便人们直观了解的reasonPhrase
作为第二个参数。
例如：


```
var body = 'hello world';
response.writeHead(200, {
'Content-Length': body.length,
'Content-Type': 'text/plain'
});```

在一次完整信息交互中此方法只能调用一次，并且必须在调用response.end()之前调用。


```
response.write(chunk, encoding='utf8')```

此方法必须在writeHead 方法调用后才可以被调用，他负责发送响应报文中的部分数据。如果要发送一个报文
体的多个部分，则可以多次调用此方法。


参数chunk 可以是一个字符串或者一个buffer。如果chunk 是一个字符串，则第二个参数指定如何将这个字符串
编码成字节流，缺省情况下，编码为'utf8'。


注意:这是一个原始格式http 报文体，和高层协议中的多段消息体编码格式({'Transfer-Encoding':'chunked'})无关。
第一次调用response.write()时，此方法会将已经缓冲的消息头和第一块消息体发送给客户。当第二次调用
response.write()的时候，node 将假定你想要以流的形式发送数据（分别发送每一个数据块并不做缓存）。这样，
其实response 对象只是缓存消息体的第一个数据块。


**response.end([data], [encoding])**


这个方法会告诉服务器此响应的所有报文头及报文体已经发出；服务器在此调用后认为这条信息已经发送完毕；
这个方法必须对每个响应调用一次。


如果指定data 参数，他就相当于调用了response.write(data, encoding)然后跟着调用了response.end()。
