# fs对文件的操作
###fs.Stats 获取文件信息
fs.stat()和fs.lstat()函数返回如下类型的对象：
```
stats.isFile()
stats.isDirectory()
stats.isBlockDevice()
stats.isCharacterDevice()
stats.isSymbolicLink() (only valid with fs.lstat())
stats.isFIFO()
stats.isSocket()```

###fs.ReadStream 读取文件
ReadStream 是一个只读流。


**fs.createReadStream(path, [options])**
fs.createReadStream 函数新建只读流对象。（请参考“只读流”章节）
options 是一个默认值如下所示的对象：


```
{
'flags': 'r'
, 'encoding': null
, 'mode': 0666
, 'bufferSize': 4 * 1024
}```

options 对象可以包含'start'和'end'参数用于从文件中读取一个范围内的数据，而不是整个文件。开始和结束都包
含并且从偏移0的位置开始，使用时必须同时指定这两个参数。
An example to read the last 10 bytes of a file which is 100 bytes long:


例子，读取一个100字节文件的最后十字节。
```
fs.createReadStream('sample.txt', {start: 90, end: 99});```
###fs.WriteStream 写入文件
WriteStream 是一个可写流。


**Event: 'open' function (fd) { }**


fd 是可写流使用的文件描述符。
fs.createWriteStream(path, [options])
此函数新建一个WriteStream 对象（参见"可写流"章节）。


options is an object with the following defaults:


options 是一个具有如下默认值的对象：


```
{
'flags': 'w'
, 'encoding': 'null'
, 'mode': 0666
}```

