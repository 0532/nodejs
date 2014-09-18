# net.Server TCP服务器模块
这个类(net.Server)是用来建立TCP 或者UNIX 服务器的。
下面有一个在8124端口等待连结的echo server 的例子：
```
var net = require('net');
var server = net.createServer(function (stream) {
stream.setEncoding('utf8');
stream.on('connect', function () {
stream.write('hello\r\n');
});
stream.on('data', function (data) {
stream.write(data);
});
stream.on('end', function () {
stream.write('goodbye\r\n');
stream.end();
});
});
server.listen(8124, 'localhost');```

如果要使用UNIX SOCKET '/tmp/echo.sock'，最后一行需要改成。
```
server.listen('/tmp/echo.sock');```

如下是该对象可以触发的事件：


**Event: 'connection'**
```
function (stream) {}```

当一个新连接建立后触发（发出）这个事件，stream 是net.Stream 类的一个实例。


**Event: 'close'**
```
function () {}```

当一个SERVER 关闭的时候触发（发出）这个事件。


**net.createServer(connectionListener)**


建立一个新的TCP SERVER。connectionListener 参数会自动设置为'connection'事件的监听函数。


**server.listen(port, [host], [callback])**


在指定端口和主机上接受一个连接请求。如果HOST 这个参数忘记写了，该SERVER 将在机器的所有IPV4地
址(INADDR_ANY)上接受连接请求。


这是一个异步函数，最后一个参数‘callback’将在服务器被绑定（应当是指当listen 正常执行完并且进入正常监听
流程后）后被调用。


**server.listen(path, [callback])**


建立一个UNIX SOCKET SERVER 并监听在指定路径上的连接。
这个函数是一个异步方法，最后一个参数‘callback’将在服务器被绑定（应当是指当listen 正常执行完并且进入正
常监听流程后）后被调用。


**server.listenFD(fd)**


建立一个SERVER 并监听在给定的文件描述符上。
这个文件描述符必须是已经在其上调用过bind(2)、listen(2)系统调用的。


**server.close()**


停止服务器，此函数是异步的。服务器在触发'close'事件后才会最终关闭。
