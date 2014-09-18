# process 进程

process 对象是一个全局对象，可以在任何地方访问。它也是一个EventEmitter 的实例。


**Event: 'exit'**


```
function () {}```


此事件在进程退出时被触发。这是一个检查模块状态的好地方（例如，做单元测试）。由于主事件循环在'exit'
返回方法之后将不会继续执行，所以计时器(timers)可能不会生效。


示例，监听exit 事件：


```
process.on('exit', function () {
process.nextTick(function () {
console.log('This will not run');
});
console.log('About to exit.');
});```


**Event: 'uncaughtException'**
```
function (err) { }```


发生未处理的异常时，此事件会被触发。如果该事件有监听函数，则不执行默认行为（默认行为将打印错误堆
栈并结束应用程序的执行）。
示例，监听'uncaughtException'事件：

```
process.on('uncaughtException', function (err) {
console.log('Caught exception: ' + err);
});
setTimeout(function () {
console.log('This will still run.');
}, 500);
// Intentionally cause an exception, but don't catch it.
nonexistentFunc();
console.log('This will not run.');```


注意，uncaughtException
 事件是一种非常原始的异常处理机制。你可以在程序中使用try/cache 来获得对程序流
的更多控制权。特别是对于要长时间执行的服务器端程序，uncaughtException 事件是个很有用的安全机制。

**Signal Events**


```
function () {}```

当进程接收到信号时被触发。要查看如SIGINT 或SIGUSR1之类的标准POSIX 信号列表，请参看参看
sigaction(2)。


监听SIGINT 信号的示例：


```
var stdin = process.openStdin();
process.on('SIGINT', function () {
console.log('Got SIGINT. Press Control-D to exit.');
});```

发送SIGIINT 信号最简单的方法是使用Control-C，大多数情况下这会终止应用程序的执行。


**process.stdout**


一个代表标准输出的流对象。


示例：console.log 的定义


```
console.log = function (d) {
process.stdout.write(d + '\n');
};```


**process.openStdin()**


打开标准输入流，返回一个只读流对象。
打开标准输入并同时监听两个事件的示例：

```
var stdin = process.openStdin();
stdin.setEncoding('utf8');
stdin.on('data', function (chunk) {
process.stdout.write('data: ' + chunk);
});
stdin.on('end', function () {
process.stdout.write('end');
});```

**process.argv**


保存命令行参数的数组。第一个参数是"node"，第二个参数是Javascript 文件的文件名，接下来是附加的命令
行参数。
```
// print process.argv
process.argv.forEach(function (val, index, array) {
console.log(index + ': ' + val);
});```

上述代码将产生如下输出：
```
$ node process-2.js one two=three four
0: node
1: /Users/mjr/work/node/process-2.js
2: one
3: two=three
4: four```

**process.execPath**


此参数为进程可执行文件的绝对路径。


例如：


```
/usr/local/bin/node```

**process.chdir(directory)**


改变进程的当前目录，失败时抛出异常。


```
console.log('Starting directory: ' + process.cwd());
try {
process.chdir('/tmp');
console.log('New directory: ' + process.cwd());
}
catch (err) {
console.log('chdir: ' + err);
}```

**process.compile(code, filename)**


同evel 方法相同，但是你可以指定文件名，这样可以更好的输出错误信息，并且运行的代码(指通过code 参数
传递的代码)无法访问本地作用域。如果编译的代码产生堆栈输出，filename 参数将会被用作这段代码的文件名。
示例，使用process.compile 和eval 执行同一段代码：

```
var localVar = 123,
compiled, evaled;
compiled = process.compile('localVar = 1;', 'myfile.js');
console.log('localVar: ' + localVar + ', compiled: ' + compiled);
evaled = eval('localVar = 1;');
console.log('localVar: ' + localVar + ', evaled: ' + evaled);
// localVar: 123, compiled: 1
// localVar: 1, evaled: 1```

process.compile 并没有访问本地作用域，所以localVar 变量并没有改变。eval 可以访问本地作用域，所以localVar
被改变了。


当代码中有语法错误时，process.compile 将会是应用程序退出。


参见：脚本（Script）章节


**process.cwd()**


返回进程的当前工作目录。


```
console.log('Current directory: ' + process.cwd());```

**process.env**


一个保存用户环境变量的对象。参看environ(7)。


**process.exit(code=0)**


使用进程退出代码（main 函数的返回值）并退出进程。如果不指定参数，exit 将使用表示成功的代码0。
示例，退出程序，并返回错误状态。


**process.exit(1);**


执行node 的shell 将会得到返回值1。


**process.getgid()**


返回进程的用户组标识。（参见getgid(2).）这个是数字形式的组ID，并非组名。


```
console.log('Current gid: ' + process.getgid());```

**process.setgid(id)**


当前进程的用户组标识。（参见setgid(2).）这个函数可以接受数字形式的组ID 或者是字符串形式的组名。如果
指定组名，此函数会阻塞进程直至将组名解析成为数字ID。
```
console.log('Current gid: ' + process.getgid());
try {
process.setgid(501);
console.log('New gid: ' + process.getgid());
}
catch (err) {
console.log('Failed to set gid: ' + err);
}```
**process.getuid()**


返回当前进程的用户标识。（参看getuid(2).）此函数返回数字形式的用户ID，而不是用户名。


```
console.log('Current uid: ' + process.getuid());```

**process.setuid(id)**


指定当前进程的用户标识。（参看setuid(2).）这个函数可以接受数字形式的用户ID 或者字符串形式的用户名。
如果指定用户名，此方法在将用户名解析成用户ID 时会阻塞。
```
console.log('Current uid: ' + process.getuid());
try {
process.setuid(501);
console.log('New uid: ' + process.getuid());
}
catch (err) {
console.log('Failed to set uid: ' + err);
}```

**process.version**


编译进可执行文件的属性，代表NODE_VERSION。
```
console.log('Version: ' + process.version);```

**process.installPrefix**


编译进可执行文件的属性，代表NODE_PREFIX。
```
console.log('Prefix: ' + process.installPrefix);```

**process.kill(pid, signal='SIGINT')**


向一个进程发送信号，参数pid 为进程ID，signal 是一个描述要发送信号的字符串，如‘SIGINT’或者‘SIGUSR1’。
如果不指定，默认发送'SIGINT'信号。更多信息请参看kill(2)。


请注意，虽然此函数名为process.kill，但是它仅仅用于发送信号，就像kill 系统调用。发送的信号做除了结束
目标进程外，还可能做其他的事情。
发送信号的示例：
```
process.on('SIGHUP', function () {
console.log('Got SIGHUP signal.');
});
setTimeout(function () {
console.log('Exiting.');
process.exit(0);
}, 100);
process.kill(process.pid, 'SIGHUP');```

**process.pid**


当前进程的ID
```
console.log('This process is pid ' + process.pid);```

**process.title**


设置、获取ps 命令中显示的名称。


**process.platform**


表示程序运行的平台，如'linux2','darwin'等。
```
console.log('This platform is ' + process.platform);```

**process.memoryUsage()**


返回一个描述Node 进程内存占用的对象。


```
var sys = require('sys');
console.log(sys.inspect(process.memoryUsage()));```

如上代码将输出：

```{ rss: 4935680
, vsize: 41893888
, heapTotal: 1826816
, heapUsed: 650472
}
```

heapTotal 和heapUsed 表示V8占用的内存。

**process.nextTick(callback)**


在事件循环的下一轮调用这个回调。此函数不是setTimeout(fn, 0)的别名，它更加高效。

    process.nextTick(function () {
    console.log('nextTick callback');
    });

**process.umask([mask])**


设置或读取进程的文件创建模式掩码，子进程会从父进程继承这个掩码。如果使用此函数设置新的掩码，则它
返回旧的掩码，否则返回当前掩码。

    var oldmask, newmask = 0644;
    oldmask = process.umask(newmask);
    console.log('Changed umask from: ' +                 oldmask.toString(8) +
            ' to ' + newmask.toString(8));



