# http.ClientRequest
http.Client 的request()方法建立并返回http.ClientRequest 对象。该对象代表一个进行中的请求（request），该请求
的消息头已经发送出去。


要获得回应，可以为request 对象增加一个'response'事件的监听器。‘response’事件将在request 对象接收到响应
头的时候被触发，'response'事件的处理函数接收一个参数，该参数是http.ClientResponse 的实例。


在'response'事件中，可以为response 对象增加监听器，监听'data'事件尤为有用。要记住，'response'事件是在接
收到回应信息体之前被触发，所以这里不需要担心信息体的第一部分不能被捕获。只要在处理'response'事件过
程中增加'data'事件监听器，信息体是肯定可以被捕获的。
```
// Good
request.on('response', function (response) {
response.on('data', function (chunk) {
console.log('BODY: ' + chunk);
});
});
// Bad - misses all or part of the body
request.on('response', function (response) {
setTimeout(function () {
response.on('data', function (chunk) {
console.log('BODY: ' + chunk);
});
}, 10);
});```

这是一个可写流

如下是此对象可以触发的事件。


**Event 'response'**
```
function (response) { }```

在响应被接收后触发。这个事件仅会被发出一次，参数response 是http.ClientResponse 的实例。


**request.write(chunk, encoding='utf8')**


发送body 中的一块。用户可以通过多次调用这个方法将请求数据包通过流的方式发送到服务器。在这个时候我
们建议使用在建立请求的时候把['Transfer-Encoding', 'chunked']放在请求头里。
参数'chunk'应当是一个数字索引的数组或字符串。


参数'encoding'是可选的，仅在chunk 为字符串的时使用。


**request.end([data], [encoding])**


完成本次请求的发送。如果消息体中的任何一个部分没有来得及发送，request.end 将把他们全部刷新到流中。
如果本次请求是分块的，这个函数将发出结束字符'0\r\n\r\n'。

如果使用参数data，就等于在调用request.write(data, encoding)之后紧接着调用request.end()。
