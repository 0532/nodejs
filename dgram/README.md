# dgram 数据报
要使用数据包SOCKET 需要调用require('dgram'),数据报一般用来处理IP/UDP 信息，但是数据报也可用在UNIX
DOMAIN SOCKETS 上


**Event: 'message'**
**function (msg, rinfo) { }**
Emitted when a new datagram is available on a socket. msg is a Buffer and rinfo is an object with the sender's address
information and the number of bytes in the datagram.


当一个SOCKET 接收到一个新的数据包的时候触发此事件， msg 是缓冲区变量,rinfo 是一个包含了发送者地址
信息以及数据报字节长度的对象.


**Event: 'listening'**
```
function () { }```

当一个SOCKET 开始监听数据报的时候触发,当UDP SOCKET 建立后就会触发这个事件。而UNIX DOMAIN
SOCKET 直到在SOCKET 上调用了bind()方法才会触发这个消息.


**Event: 'close'**

```
function () { }```

当一个SOCKET 使用close()方法关闭时触发此事件.在此事件之后此SOCKET 不会有任何消息事件被触发.


**dgram.createSocket(type, [callback])**


建立一个指定类型的数据报SOCKET,有效类型有:udp4,udp6,unix_dgram
callback 作为一个可选项，可作为message 事件的监听器被加入。


**dgram.send(buf, offset, length, path, [callback])**


对于unix domain datagram xockets 来说,他的目标地址是一个使用文件系统表示的路径名,callback 作为一个可选
项会在系统调用sendto 完毕后被触发。除非callback 被触发，否则重复使用buf 是很不安全的。要注意除非这
个socket 已经使用bind()方法绑定到一个路径名上，否则这个SOCKET 无法接收到任何信息。


下面是一个通过unix domain socket /var/run/syslog 发送消息到syslogd 的例子：


```
var dgram = require('dgram'),
message = new Buffer("A message to log.");
client = dgram.createSocket("unix_dgram");
client.send(message, 0, message.length, "/var/run/syslog",
function (err, bytes) {
if (err) {
throw err;
}
console.log("Wrote " + bytes + " bytes to socket.");
});```

从MESSAGE 中偏移为0的地方开始，长度为MESSAGE.LENGTH 的这些内容通过/var/run/syslog 发送系统调
用发送后，将调用CALLBACK，如果有错误则抛出异常，否则console.log 实际发送了多少个字节。

**dgram.send(buf, offset, length, port, address, [callback])**


对于UDPSOCKETS 来说，目标端口和IP 地址是必须要指定的，可以用字符串来指定地址参数，并且这个参数
是可以通过DNS 解析的，CALLBACK 作为可选项可以检测到任何DNS 错误和是否BUF 重复使用了.请记住
DNS 搜索将会使SEND 动作最少延迟到下一个执行时间片发生， ，唯一能确定已经SEND 得方法是使用
CALLBACK


下面是一个发送UDP 数据包到本机一个随机端口的例子


```
var dgram = require('dgram'),
message = new Buffer("Some bytes");
client = dgram.createSocket("udp4");
client.send(message, 0, message.length, 41234, "localhost");
client.close();```

**dgram.bind(path)**


只有在Unxi DOMAIN DATAGRAM SOCKET 中使用,开始在一个指定路径上监听一个SOCKET 过来的的数据
报。要记得，客户端可以不是用BIND()方法而直接调用SEND()方法，但是不使用BIND()方法是无法接收到任
何信息的。


下面是一个使用UNIX DOMAIN 数据包服务器来做接受信息回显的例子：


```
var dgram = require("dgram");
var serverPath = "/tmp/dgram_server_sock";
var server = dgram.createSocket("unix_dgram");
server.on("message", function (msg, rinfo) {
console.log("got: " + msg + " from " + rinfo.address);
server.send(msg, 0, msg.length, rinfo.address);
});
server.on("listening", function () {
console.log("server listening " + server.address().address);
})
server.bind(serverPath);```

下面是一个UNIX DOMAIN DATAGRAM 客户端与服务器交互的例子

```
var dgram = require("dgram");
var serverPath = "/tmp/dgram_server_sock";
var clientPath = "/tmp/dgram_client_sock";
var message = new Buffer("A message at " + (new Date()));
var client = dgram.createSocket("unix_dgram");
client.on("message", function (msg, rinfo) {
console.log("got: " + msg + " from " + rinfo.address);
});
client.on("listening", function () {
console.log("client listening " + client.address().address);
client.send(message, 0, message.length, serverPath);
});
client.bind(clientPath);```

dgram.bind(port, [address])


对于UDP SOCKETS，这个方法会在指定端口和可选地址上监听，如果地址没有指定，则系统会尝试监听所有
有效地址。


下面是一个监听在41234端口的UDP 服务器的例子
```
var dgram = require("dgram");
var server = dgram.createSocket("udp4");
var messageToSend = new Buffer("A message to send");
server.on("message", function (msg, rinfo) {
console.log("server got: " + msg + " from " +
rinfo.address + ":" + rinfo.port);
});
server.on("listening", function () {
var address = server.address();
console.log("server listening " +
address.address + ":" + address.port);
});
server.bind(41234);
// server listening 0.0.0.0:41234```

**dgram.close()**


这个方法关闭非延迟的SOCKET 并且停止在其上监听数据。即使没有调用BIND()方法UDP SOCKET 也会自动
监听消息。


**dgram.address()**


返回包含SOCKET 地址信息的一个对象，对于UDP SOCKETS 来说，这个对象将包含地址和端口，对于UNIX
DOMAIN SOCKETS 来说，这个对象仅包含地址。

**dgram.setBroadcast(flag)**


设置或者清除SO_BROADCAST 选项，当这个选项设置后，UDP 包可以发送到本地接口的广播地址。


**dgram.setTTL(ttl)**


设置IP_TTL 这个选项，TTL 表示“存活时间”，但是在这个上下文环境中，他也可以指定IP 的HOPS（每个节
点在转发数据包时的消耗。如果Hop limit 消耗到0，则取消数据包）来确定一个数据包大致允许经过多少节点。
每经过个路由器或者网关都会减少TTL 数值，如果TTL 被一个路由器减少到0，这个数据报将不会继续转发，
修改TTL 数值经常用来当网络探针或者作为数据多播使用
ttl 用来设置HOPS 的数值从1到255，大多数系统缺省会设置为64
