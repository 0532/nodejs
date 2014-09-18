# net.Stream TCP流模块
这个对象是对TCP 或者UNIX SOCKET 的抽象，它实现了全双工的流接口。net.Stream 可以由用户手动建立，
并且作为一个客户端来使用(和connect())，也可以被node 建立并通过服务器的'connection'事件传递给用户。(译
注：如http.Server 的connection 事件，会将net.Stream 的实例当作参数传递给响应函数)
net.Stream 实例会发出下列事件：


**Event: 'connect'**
```
function () { }```

当成功建立连接后触发此事件事件。详见connect()。


**Event: 'secure'**
```
function () { }```

当一个stream 与其对等端安全建立一个SSL 握手后触发。


**Event: 'data'**
```
function (data) { }```

当接收到数据时触发该事件，数据会是Buffer 或者String，数据的编码通过stream.setEncoding()来设计(查看可
读流那部分文章来获得更多信息)。


**Event: 'end'**
```
function () { }```

当stream 发出一个FIN 包后触发此事件。这个事件发出后准备状态会变为‘只写’(writeOnly)。当这个事件被发出
后，唯一能做的事情或许只是call stream.end()了。


**Event: 'timeout'**
```
function () { }```

当流因为不活动而超时时触发这个事件。这是唯一一个因为stream 空闲而通知的事件，这个时候用户必须手动
关闭这个连接。
参见：stream.setTimeout()


**Event: 'drain'**
```
function () { }```

当写缓冲区变空的时候触发这个事件，这个事件可以用来控制/调节上传。

Event: 'error'
function (exception) { }
当发生一个错误时候触发。‘close’事件将跟随这个事件被发出。
**Event: 'close'**
```
function (had_error) { }```

当stream 被完全关闭时发出这个事件。参数had_error 是一个用来标示stream 关闭是否是因为传输错误所导致
的标志。


```
net.createConnection(port, host='127.0.0.1')```

构造一个新的stream 对象并且打开一个stream 到指定的端口和主机，如果第二个参数没有写，则假设主机为
localhost


建立连接后触发connect 事件。


**stream.connect(port, host='127.0.0.1')**


在指定端口和主机打开一个stream。createConnection()也可以建立连接，所以通常我们并不需要使用这个方法。
只有在一个stream 被关闭，并且你希望重新使用这个对象连接到其他主机的时候我们才用这个方法。


这个函数是异步的。在连接建立之后会触发‘connect’事件。如果在连接过程中产生问题，将产生'error'事件，而
不会被触发'connect'。


**stream.remoteAddress**


这个字符串代表远程计算机的IP 地址，例如'74.125.127.100' 或者'2001:4860:a005::68'。
这个成员变量只存在于服务器端连接。


**stream.readyState**


stream.readyState 可以是'closed', 'open', 'opening', 'readOnly' 'writeOnly' 中的一个。


**stream.setEncoding(encoding='null')**


为接受到的数据设置编码格式(只能'ascii', 'utf8', 'base64'中的一个)。


**stream.setSecure([credentials])**


配合crypto 模块提供的私钥和证书（在peer authentication 中是CA 证书），此方法可以为stream 提供https 支持。


如果credentials 包含一个或多个数字认证中心证书（CA certificates），则stream 将请求对等待方提交一个客户端
证书作为HTTPS 连接握手的一部分，证书的合法性及内容可以通过verifyPeer()及getPeerCertificate 来查看。


**stream.verifyPeer()**


根据被信任的证书列表或对方连接证书上下文的验证结果返回true 或false。


**stream.getPeerCertificate()**


返回用JSON 结构详尽表述的对等方证书，其中包含一个证书的‘主题’，‘发布者’，'有效来源','有效目标'。


**stream.write(data, encoding='ascii')**


通过stream 发送数据,第二个参数指定这个字符串实例的编码，缺省为ASCII，因为实用UTF8编码比较慢。


如果全部数据安全写入内核缓冲区则返回true，如果全部或者部分数据仍然在用户空间排队则返回false, 当缓
冲区再次空闲的时候'drain'事件会被触发。


**stream.end([data], [encoding])**
半关闭stream，也就是说，steram 发出一个FIN 包。这个时候可能服务器还会发出一些数据。当call 这个函数
后,readyState 会变成'readOnly'。
如果使用了data 这个参数, 这就相当于call stream.write(data, encoding), 其次后面跟随着
stream.end().stream.destroy()
请确保在这个stream 上没有任何I/O 活动。只有在错误发生的时候（为了调试错误）才需要调用此函数。


**stream.pause()**


暂停数据的读取.更确切的说，'data'事件将不会被发出,这个方法在控制上传的时候非常有用。


**stream.resume()**


当恢复读取数据。

**stream.setTimeout(timeout)**


设置timeout 这么多毫秒作为stream 的超时时间，默认情况下net.Stream 没有超时时间。
当超时事件被触发，stream 将受到一个‘timeout’事件，但是连接将不会被断掉，用户必须执行end()或者destroy
来结束这个stream。


如果timeout 为0，会禁用超时。


**stream.setNoDelay(noDelay=true)**


禁止Nagle algorithm(Nagle 运算模式被设计用来减少LAN 和其它网络拥塞)，缺省情况下TCP 连接使用Nagle
algorithm，此模式会在真正将数据发出前将其缓冲起来。设置noDelay 将在每次call stream.write()时立刻将数据
连续发出。


stream.setKeepAlive(enable=false, [initialDelay])


允许/禁止keep-alive 功能，在一个空闲stream 首次收到keepalive 之前可以设置killalive 的延时。设置
initialDelay(毫秒)变量将设置接收到最后的数据包和首次keepalive 探测之间的时差。设置为0则保持缺省（或者
上一次的）设置的数值不变。
