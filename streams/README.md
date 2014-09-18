# Streams 流
stream 是一个抽象接口，node 中有很对象实现了这个接口。例如，对http 服务器发起请求的request 对象就是
一个stream，还有stdout（标准输出）。Stream 可以是只读、可写，也可以同时可读可写。所有的Stream 对象
都是EventEmitter 的实例。


##Readable Stream 只读流

一个只读流有如下方法、成员、和事件。


Event: 'data'

```
function (data) { }```


'data'事件的参数是Buffer（默认情况下），如果调用过setEncoding()方法，则参数为一个字符串。


**Event: 'end'**

```
function () { }```

此事件在流遇到EOF(在TCP 中为FIN)时被触发，表示该流不会再有数据（不会再次触发'data'事件）。如果该流
也是可写流，则它还可以继续写入。


**Event: 'error'**

```
function (exception) { }```

在收取数据出错时被触发。


**Event: 'close'**

```
function () { }```


内部的文件描述符被关闭时被触发，并不是所有的流都会触发此事件。（例如，一个进入的(incoming)HTTP 请
求将不会触发'close'事件）。

**Event: 'fd'**

```
function (fd) { }
```


当数据流接收到文件描述符信息时触发该事件（一个文件数据流包含两部分信息：文件描述符信息和文件的数
据信息）。本事件只支持Unix 数据流，其他类型的流不会触发该事件。

**stream.readable**

一个布尔值，默认为true。当遇到错误或流读到结尾或者调用destory()函数后，该值被设置为false。

**stream.setEncoding(encoding)**

该函数设置data 事件返回字符串而不是Buffer 对象。编码类型可以设置为"utf8"，"ascii"或"base64"。

**stream.pause()**

暂停触发data 事件。

**stream.resume()**


恢复触发'data'事件。

**stream.destroy()**

关闭内部的文件描述符。这样该流将不会再触发任何事件。

##Writable Stream 可写流

一个可写流具备以下方法、成员、和事件。

**Event: 'drain'**

```
function () { }```

在一个wrire() 方法被调用并返回false 后触发，表明可以安全的再次写入该stream。


**Event: 'error'**

```
function (exception) { }
```

在异常发生赤错误时被触发。

**Event: 'close'**

```
function () { }```

当底层的文件描述符已终止时发出。

**stream.writeable**

一个boolean 值，缺省为true ，但是在一个'error'产生或是end() / destroy() 被调用后，会变为false 。

**stream.write(string, encoding='utf8', [fd])**

使用指定的编码将字符串字符串写入到流中。如果字符串已被刷新到内核缓冲区，返回true。返回false 则表明
内核缓冲区已满，数据将在未来被发送出去。'drain'事件用来通知内核缓冲区何时为空。此方法的默认编码为
'utf8'。

如果指定了可选参数fd，它将被当做一个文件描述符并通过流来发送。它只支持UNIX 流，否则会被忽略且没
有任何提示。当用这种方式发送文件描述符时，在流清空之前关闭文件描述符可能导致发送出非法的描述符。

**stream.write(buffer)**

同上，除了使用一个原始缓冲区。

**stream.end()**

通过EOF 或FIN 来终止流。

**stream.end(string, encoding)**

根据指定的编码发送字符串，并通过EOF 或FIN 来终止流。这对于减少发送数据包的数量是非常有用的。

**stream.end(buffer)**

同上，但使用一个缓冲区。

**stream.destroy()**

终止底层的文件描述符，此后流不再发出任何事件。
