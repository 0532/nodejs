# Script 脚本
Script 类可以编译执行JavaScript 代码。你可以用以下方式访问Script 类：
```
var Script = process.binding('evals').Script;```

JavaScript 代码可以被编译、立刻执行或者编译、保存、延时执行。


**Script.runInThisContext(code, [filename])**


同process.compile 函数类似，Script.runInThisContext 函数编译执行code 参数包含的代码并返回结果，就如同这
些代码是从filename 参数指定文件中加载的一样。这些代码不能访问本地作用域。filename 参数是可选的。（译
注：filename 参数的作用是为了更好的输出错误信息）


示例：演示使用Script.runInThisContext 函数和eval 函数执行同一段代码：
```
var localVar = 123,
usingscript, evaled,
Script = process.binding('evals').Script;
usingscript = Script.runInThisContext('localVar = 1;',
'myfile.js');
console.log('localVar: ' + localVar + ', usingscript: ' +
usingscript);
evaled = eval('localVar = 1;');
console.log('localVar: ' + localVar + ', evaled: ' +
evaled);
// localVar: 123, usingscript: 1
// localVar: 1, evaled: 1```

**Script.runInThisContext** 函数执行的代码并不访问本地作用域，所以localVar 变量的值并没有改变。eval 函数执
行的代码可以访问本地作用域，所以localVal 的值被改变了。


如果代码有语法错误，Script.runInThisContext 会输出错误信息到控制台（stderr）并抛出异常。

**Script.runInNewContext(code, [sandbox], [filename])**


Script.runInNewContext 将代码编译并在sandbox 参数指定的作用域内执行代码并返回结果，就如同代码是从文
件中加载的一样。执行的代码并不访问本地作用域，sandbox 参数指定的对象将作为代码执行的全局对象。
sandbox 和filename 参数都是可选的。


例子：编译并执行一段代码，这段代码递增并新建一个全局变量。这些全局变量都保存在sandbox 中。
```
var sys = require('sys'),
Script = process.binding('evals').Script,
sandbox = {
animal: 'cat',
count: 2
};
Script.runInNewContext(
'count += 1; name = "kitty"', sandbox, 'myfile.js');
console.log(sys.inspect(sandbox));
// { animal: 'cat', count: 3, name: 'kitty' }```

请注意，执行不信任的代码(untrusted code)是一项需要技巧的工作。Script.runInNewContext 函数非常有用，它可
以在一个独立的线程中执行不信任的代码防止全局变量被意外修改。


如果代码有语法错误，Script.runInThisContext 会输出错误信息到控制台（stderr）并抛出异常。


**new Script(code, [filename])**


新建Script 对象会编译code 参数指定的代码，就如同代码是从filename 参数指定的文件中加载的一样。和其他
函数不同的是，它将返回一个代表经过编译的代码的Script 对象，这个对象可以使用下面介绍的函数执行内部
编译好的代码。这个script 对象并不绑定到任何全局对象，但是可以在运行时绑定到指定对象，每次绑定仅在
本次运行时生效。filename 参数是可选的。


如果代码有语法错误，new Script emits 会输出错误信息到控制台（stderr）并抛出异常。


**script.runInThisContext()**


这个函数和Script.runInThisContext 函数类似（对象名首字母'S'的大小写不同），不同的是此函数是Script 对象的
方法。script.runInThisContext 函数执行对象中的代码并返回结果。执行的代码并不会访问本地作用域，但是可
以访问全局作用域（v8: in actual context）。


例子：使用script.runInThisContext 函数实现代码的一次编译多次执行。
```
var Script = process.binding('evals').Script,
scriptObj, i;
globalVar = 0;
scriptObj = new Script('globalVar += 1', 'myfile.js');
for (i = 0; i < 1000 ; i += 1) {
scriptObj.runInThisContext();
}
console.log(globalVar);
// 1000```
**script.runInNewContext([sandbox])**

此函数和Script.runInNewContext 函数类似（对象名首字母'S'的大小写不同），不同的是此函数是Script 对象的
方法。script.runInNewContext 函数将sandbox 参数指定的对象作为全局对象执行代码，并返回结果。执行的代
码并不访问本地作用域。sandbox 参数是可选的。


例子：编译并执行一段代码，这段代码递增并新建一个全局变量，这些全局变量都保存在sandbox 中。然后多次执行这段代码，这些全局变量都保存在沙盒(sandbox)中。


```
var sys = require('sys'),
Script = process.binding('evals').Script,
scriptObj, i,
sandbox = {
animal: 'cat',
count: 2
};
scriptObj = new Script(
'count += 1; name = "kitty"', 'myfile.js');
for (i = 0; i < 10 ; i += 1) {
scriptObj.runInNewContext(sandbox);
}
console.log(sys.inspect(sandbox));
// { animal: 'cat', count: 12, name: 'kitty' }```

请注意，执行不信任的代码(untrusted code)是一项需要技巧的工作。script.runInNewContext 函数非常有用，它可
以在一个单独的线程中执行不信任的代码防止全局变量被意外修改。

####File System 文件系统
(注：同步与异步方式是指同步或异步于程序执行，并非函数彼此之间的同步关系。)
文件的I/O 是由标准POSIX 函数封装而成。需要使用"require('fs')"操作这个类。所有的方法设有异步方式和同步
方式。


异步形式下的方法其最后一个参数，总是一个完整的回调函数（callback）。这个回调函数有那些参数，就取决
于异步方法怎么送入参数，但通常来说，第一个送入的参数是异常对象。如果是没有任何问题的操作，那么这
个异常对象就变为null 或者undefined，表示操作正常。
异步方式的例子：


```
var fs = require('fs');
fs.unlink('/tmp/hello', function (err) {
if (err) throw err;
console.log('successfully deleted /tmp/hello');
});```

同步方式的例子：


```
var fs = require('fs');
fs.unlinkSync('/tmp/hello')
console.log('successfully deleted /tmp/hello');```

异步函数没有一定的顺序，所以以下例子容易发生错误：
```
fs.rename('/tmp/hello', '/tmp/world', function (err) {
if (err) throw err;
console.log('renamed complete');
});
fs.stat('/tmp/world', function (err, stats) {
if (err) throw err;
console.log('stats: ' + JSON.stringify(stats));
});```

这有可能fs.state 于fs.rename 前执行。正确的做法是嵌套回传函数。
```
fs.rename('/tmp/hello', '/tmp/world', function (err) {
if (err) throw err;
fs.stat('/tmp/world', function (err, stats) {
if (err) throw err;
console.log('stats: ' + JSON.stringify(stats));
});
});```

当执行动作繁杂时，强烈建议使用异步方式调用此类。同步方式在其完成之前将会阻挡一切随后的动作，这代
表搁置所有连接。


**fs.rename(path1, path2, [callback])**


异步命名(rename(2))。只传递异常给回调函数。


**fs.renameSync(path1, path2)**


同步命名(rename(2))。

**fs.truncate(fd, len, [callback])**


异步截断(ftruncate(2))。只传递异常给回调函数。


**fs.truncateSync(fd, len)**


同步截断(ftruncate(2))。


**fs.chmod(path, mode, [callback])**


异步更改文件权限(chmod(2))。只传递异常给回调函数。

**fs.chmodSync(path, mode)**


同步更改文件权限(chmod(2))。


**fs.stat(path, [callback])**


利用路径异步读取属性(stat(2))。回调函数的第二个参数是fs.Stats 对象(err, stats)，例如：
```
{ dev: 2049
, ino: 305352
, mode: 16877
, nlink: 12
, uid: 1000
, gid: 1000
, rdev: 0
, size: 4096
, blksize: 4096
, blocks: 8
, atime: '2009-06-29T11:11:55Z'
, mtime: '2009-06-29T11:11:40Z'
, ctime: '2009-06-29T11:11:40Z'
}```

参考fs.Stats 部份以取得详细资料。


**fs.lstat(path, [callback])**


利用路径异步读取属性(lstat(2))。如果这个文件参数是一个符号连接，则返回该符号连接的属性。回调函数的第
二个参数是fs.Stats 对象。(err, stats)


**fs.fstat(fd, [callback])**
利用存在的指标异步读取属性(fstat(2))。回调函数的第二个参数是fs.Stats 对象。(err, stats)


**fs.statSync(path)**


同步读取属性(stat(2))。返回fs.Stats。


**fs.lstatSync(path)**


利用路径同步读取属性(lstat(2))。返回fs.Stats。


**fs.fstatSync(fd)**


利用存在的指标同步读取属性(fstat(2))。返回fs.Stats。


**fs.link(srcpath, dstpath, [callback])**

异步建立连接(link(2))。只传递异常给回调函数。


**fs.linkSync(dstpath, srcpath)**


同步建立连接(link(2))。


**fs.symlink(linkdata, path, [callback])**


异步建立符号连接(symlink(2))。只传递异常给回调函数。


**fs.symlinkSync(linkdata, path)**


同步建立符号连接(symlink(2))。


**fs.readlink(path, [callback])**


异步读取连接(readlink(2))。回调函数的第二个参数是已解析的文件路径。(err, resolvedPath)


**fs.readlinkSync(path)**


同步读取连接(readlink(2))。返回已解析的文件路径。


**fs.realpath(path, [callback])**


异步读取絶对的路径名称(realpath(2))。回调函数的第二个参数是已解析的文件路径。(err, resolvedPath)


**fs.realpathSync(path)**


同步读取絶对的路径名称(realpath(2))。返回已解析的文件路径。


**fs.unlink(path, [callback])**

异步删除连接(~=删除文件)(unlink(2))。只传递异常给回调函数。

**fs.unlinkSync(path)**


同步删除连接(~=删除文件)(unlink(2))。


**fs.rmdir(path, [callback])**


异步删除目录(rmdir(2))。只传递异常给回调函数。


**fs.rmdirSync(path)**


同步删除目录(rmdir(2))。


**fs.mkdir(path, mode, [callback])**

异步建立目录(mkdir(2))。只传递异常给回调函数。


**fs.mkdirSync(path, mode)**


同步建立目录(mkdir(2))。


**fs.readdir(path, [callback])**


异步读取目录中的内容(readdir(3))。回调函数的第二个参数是以阵列构成的目录内对象的名称('.'与'..'除外)。(err,
files)


**fs.readdirSync(path)**


同步读取目录中的内容(readdir(3))。返回以阵列构成的目录内对象名称('.'与'..'除外)。


**fs.close(fd, [callback])**


异步结束(close(2))。只传递异常给回调函数


**fs.closeSync(fd)**


同步结束(close(2))。


**fs.open(path, flags, mode=0666, [callback])**


异步开启文件，详阅open(2)。标签可为'r', 'r+', 'w', 'w+', 'a', 或'a+'。回调函数的第二个参数是指标。(err, fd)


**fs.openSync(path, flags, mode=0666)**


同步开启文件。


**fs.write(fd, buffer, offset, length, position, [callback])**


透过指标(fd)写入缓冲区至文件。
offset 偏移 和 length 长度 决定哪一部份的缓冲区被写入。

position 写入位置 若 position 为空，则写入至现存位置。详阅 pwrite(2)。
回调函数的第二个参数是写入动作的数据大小(bytes)。(err, written)


**fs.writeSync(fd, buffer, offset, length, position)**


fs.write(缓冲区)的同步方式。返回写入动作的数据大小。


**fs.writeSync(fd, str, position, encoding='utf8')**


fs.write(字串)的同步方式。返回写入动作的数据大小。

**fs.read(fd, buffer, offset, length, position, [callback])**


透过指标(fd)读取数据。


buffer 是读取的数据的存放位置。


offset 是标注哪里开始写入缓冲区。


length 是以整数型态(INT)标注读取的数据大小。


position 是以整数型态(INT)标注文件的读取位置。若 position 为空，则由现存位置读取。
回调函数的第二个参数是读取动作的数据大小(bytes)。(err, bytesRead)


**fs.read(fd, length, position, encoding, [callback])**


透过指标(fd)读取数据。


length 是以整数型态(INT)标注读取的数据大小。


position 是以整数型态(INT)标注文件的读取位置。若
position 为空，则由现存位置读取。


encoding 是读取数据的预期编码。
回调函数的第二个参数是读数的数据而第三个参数是读取动作的数据大小(bytes)。(err, str, bytesRead)


**fs.readSync(fd, buffer, offset, length, position)**


fs.read(缓冲区)的同步方式。返回读取动作的数据大小。


**fs.readSync(fd, length, position, encoding)**


fs.read(字串)的同步方式。返回读取动作的数据大小。


**fs.readFile(filename, [encoding], [callback])**


透过文件路径异步读取内容，例子：

```
fs.readFile('/etc/passwd', function (err, data) {
if (err) throw err;
console.log(data);
});```

回调函数的第二个参数是文件内容。(err, datad)
若编码(encoding)没有被指定，则返回原始缓冲区。


**fs.readFileSync(filename, [encoding])**


fs.readFile()的同步方式。返回文件内容。


若编码(encoding)被指定，则返回字串，反之则返回原始缓冲区。


**fs.writeFile(filename, data, encoding='utf8', [callback])**

异步写入数据至文件，data(数据)可以为字串或缓冲区。
例子：
```
fs.writeFile('message.txt', 'Hello Node', function (err) {
if (err) throw err;
console.log('It\'s saved!');
});```

**fs.writeFileSync(filename, data, encoding='utf8')**


fs.writeFile()的同步方式。


**fs.watchFile(filename, [options], listener)**


观察文件异变。文件异动时会触发监听函数。


第二个参数是可选的。选项(options)参数应为对象(object)，当中包含一布林值(BOOL)持续检查(persistent)与检测
相隔时间(interval)(单位为毫秒)。


传递给监听函数的参数分别是当前状态对象(curr)以及前次状态对象(prev)。


```
fs.watchFile(f, function (curr, prev) {
console.log('the current mtime is: ' + curr.mtime);
console.log('the previous mtime was: ' + prev.mtime);
});```

stat 对象是fs.Stat 的实例。


**fs.unwatchFile(filename)**


停止观察文件异变。
