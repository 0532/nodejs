# Buffers 缓存对象
纯粹的Javascript 对Unicode 很友好，但是操作二进制数据就不怎么在行了。处理TCP 数据流或者文件时，必
须要操作二进制数据流。node 提供了一些方法来创建、操作和接收二进制数据流。


原始的数据保存在Buffer 类的实例中。Buffer 类似于一个整数数组，不同之处在于它和在V8内存堆之外分配的
一段内存数据相对应。Buffer 对象的大小不能调整。你可以通过"require('buffer').Buffer"来使用这个类。


Buffer 对象是全局对象。


Buffer 和Javascirpt 中string 对象之间的转换需要指定编码方式。如下是node 支持的各种编码方式：


    'ascii' - 应用于7位的ASCII 数据。这种编码方式速度很快，它会删除字节的高位。
    'utf8' - Unicode 字符。许多网页和其他文档使用UTF-8。
    'binary' - 一种只使用每个字符前8个字节将原始的二进制数据编码进字符串的方式。
    这个方式已经废弃，应当尽量使用buffer 对象。这个编码将会在未来的node 中删除。


**new Buffer(size)**

创建指定大小的buffer 对象。

**new Buffer(array)**

从数组新建buffer 对象。

**new Buffer(str, encoding='utf8')**

新建一个保存指定字符串的buffer 对象。

**buffer.write(string, offset=0, encoding='utf8')**

使用指定的编码方式将字符串从指定偏移开始写入buffer，然后返回实际写入的大小。如果buffer 空间不足，
则只会写入部分字符串。在本例中使用'utf8'编码，这种方式不会写入半个字符。

示例：将一个utf8字符串写入buffer，然后打印出来

```
buf = new Buffer(256);
len = buf.write('\u00bd + \u00bc = \u00be', 0);
console.log(len + " bytes: " + buf.toString('utf8', 0, len));
// 12 bytes: ½ + ¼ = ¾```

**buffer.toString(encoding, start=0, end=buffer.length)**

解码buffer 数据并使用指定的编码返回字符串，转换从start 参数指定的位置开始，到end 结束。
参看上面buffer.write()的例子。

**buffer[index]**

获取或设置指定的字节。返回值代表一个字节，所以返回值的合法范围是十六进制0x00到0xFF 或者十进制0至
255。


例如：将一个ASCII 字符串复制进buffer，每次一个字节：

```
str = "node.js",
buf = new Buffer(str.length),
i;
for (var i = 0; i < str.length ; i += 1) {
buf[i] = str.charCodeAt(i);
}
console.log(buf);
// node.js```


**Buffer.byteLength(string, encoding='utf8')**

返回字符串的实际字节数。这个函数和String.prototype.length 不同，后者返回字符串的字符数。


```
str = '\u00bd + \u00bc = \u00be';
console.log(str + ": " + str.length + " characters, " +
Buffer.byteLength(str, 'utf8') + " bytes");
// ½ + ¼ = ¾: 9 characters, 12 bytes```

**buffer.length**

buffer 的大小（以字节为单位）。请注意，这个不是存放内容的大小，而是分配给buffer 对象的内存大小。这个
大小不随buffer 中存放内容的多少而改变。

```
buf = new Buffer(1234);
console.log(buf.length);
buf.write("some string", "ascii", 0);
console.log(buf.length);
// 1234
// 1234```


**buffer.copy(targetBuffer, targetStart, sourceStart, sourceEnd=buffer.length)**

在两个buffer 之间执行内存拷贝。

例如：新建两个buffer 对象，然后将buf1中16至19字节拷贝到buf2中第八字节开始的空间中。

```
buf1 = new Buffer(26),
buf2 = new Buffer(26),
i;
for (var i = 0 ; i < 26 ; i += 1) {
buf1[i] = i + 97; // 97 is ASCII a
buf2[i] = 33; // ASCII !
}
buf1.copy(buf2, 8, 16, 20);
console.log(buf2.toString('ascii', 0, 25));```


**buffer.slice(start, end)**

返回和老的buffer 引用同一段内存的新buffer 对象，但是开始和结束的位置由start 和end 参数指定。
修改新的buffer 对象将会改动原来的buffer。

例如：使用字母表建立一个buffer 对象，并剪切出一个新的buffer，然后修改原始buffer 的一个字节。

```
buf1 = new Buffer(26), buf2,
i;
for (var i = 0 ; i < 26 ; i += 1) {
buf1[i] = i + 97; // 97 is ASCII a
}
buf2 = buf1.slice(0, 3);
console.log(buf2.toString('ascii', 0, buf2.length));
buf1[0] = 33;
console.log(buf2.toString('ascii', 0, buf2.length));
// abc
// !bc```



