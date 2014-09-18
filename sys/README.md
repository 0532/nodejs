# 通过sys访问模块


本章介绍的函数包含在sys模块中，可以通过require('sys')访问他们。


**sys.print(string)**


此函数和console.log()类似，只是它不输出结尾的换行符。


```
require('sys').print('String with no newline');```

**sys.debug(string)**


同步输出函数，此函数将阻塞进程并将字符串打印到标准错误输出（stderr）。
```
require('sys').debug('message on stderr');```
**sys.log(string)**

将字符串输出至标准输出（stdout,就是控制台），附加时间戳。
```
require('sys').log('Timestmaped message.');```

**sys.inspect(object, showHidden=false, depth=2)**


将对象转化为字符串的形式返回，对调试非常有用。


如果showHidden 参数设定为true，则对象的非枚举属性也会被转化。
如果指定depth 参数，它告诉解析器(inspectter)格式化对象的时候要递归的次数。这个参数对于解析(inspecting)
复杂的对象很有用。


默认只递归两次。要想无限递归，请传递null。
例子，解析sys 对象的所有属性：
```
var sys = require('sys');
console.log(sys.inspect(sys, true, null));```
**sys.pump(readableStream, writeableStream, [callback])**
Experimental 实验性的


从readableStream 读取数据并写入writableStream。如果writeableStream.write(data)返回flase，readableStream 将
暂停，直到writableStream 的drain 事件被触发。当writableStream 关闭或者错误发生时，回调函数（第三个参
数）会被调用，并接受一个表示错误的参数。
