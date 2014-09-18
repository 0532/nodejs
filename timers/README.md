# Timers 计时器
**setTimeout(callback, delay, [arg], [...])**


设置延时delay 毫秒之后执行回调函数(callback)。该函数返回timeoutId，可以使用clearTimeout()清除定时。你
也可以传递额外的参数给回调函数。


**clearTimeout(timeoutId)**


清除指定的定时。


**setInterval(callback, delay, [arg], [...])**


设置重复延时调用callback。该函数返回intervalId，可以使用clearTimeout()清除定时。你也可以传递可选的
参数给回调函数。

.end();
**clearInterval(intervalId)**

清楚指定的重复延时回调。


###Child Processes 子进程
node 通过ChildProcess 类提供全面的popen(3)功能。
程序可以通过子进程的标准输入(stdin)、标准输出(stdout)、标准错误输出(stderr)以完全非阻塞的形式传递数据。


你可以使用require('child_process').spawn()创建子进程。
每个子进程总是带有三个流对象：child.stdin, child.stdout 和child.stderr。
每个ChildProcess 类也是一个事件触发器。


**Event: 'exit'**
```
function (code, signal) {}```

此事件在子进程结束后被触发。如果进程正常结束，code 参数的值就是子进程退出后的返回值，否则此参数为
null。如果进程因为信号而终止，参数signal 就是信号的名称，否则此参数为null。
触发此事件之后，node 将不再触发'output'和'error'事件。
See waitpid(2).


**child.stdin**


可写流(Writable Stream)，代表子进程的标准输入(stdin)。使用end()函数关闭此流(stream)，通常会终止子进程。

**child.stdout**


只读流(Readable Stream)，代表子进程的标准输出(stdout)。
**child.stderr**


只读流(Readable Stream)，代表子进程的标准错误输出(stderr)。


**child.pid**


子进程的PID
Example:

```
var spawn = require('child_process').spawn,
grep = spawn('grep', ['ssh']);
console.log('Spawned child pid: ' + grep.pid);
grep.stdin.end();```

**child_process.spawn(command, args=[], [options])**


使用指定的命令行参数创建新线程。如果不指定参数，args 默认为空数组。
第三个参数用于指定一些额外的选项，默认值如下：


```
{ cwd: undefined
, env: process.env,
, customFds: [-1, -1, -1]
}```

cwd 可以用于指定新进程的工作目录，env 指定新进程可见的环境变量，而customFds 则可以将新进程的
[stdin,stdout,stderr]绑定到指定的流，-1指创建新的流。


例子：执行命令'ls -lh /usr'并捕获标准输出、标准错误输出和退出代码（程序退出时，main 函数返回的代码）。
```
var sys = require('sys'),
spawn = require('child_process').spawn,
ls = spawn('ls', ['-lh', '/usr']);
ls.stdout.on('data', function (data) {
sys.print('stdout: ' + data);
});
ls.stderr.on('data', function (data) {
sys.print('stderr: ' + data);
});
ls.on('exit', function (code) {
console.log('child process exited with code ' + code);
});```
例子： 实现'ps ax | grep ssh'命令。
```
var sys = require('sys'),
spawn = require('child_process').spawn,
ps = spawn('ps', ['ax']),
grep = spawn('grep', ['ssh']);
ps.stdout.on('data', function (data) {
grep.stdin.write(data);
});
ps.stderr.on('data', function (data) {
sys.print('ps stderr: ' + data);
});
ps.on('exit', function (code) {
if (code !== 0) {
console.log('ps process exited with code ' + code);
}
grep.stdin.end();
});
grep.stdout.on('data', function (data) {
sys.print(data);
});
grep.stderr.on('data', function (data) {
sys.print('grep stderr: ' + data);
});
grep.on('exit', function (code) {
if (code !== 0) {
console.log('grep process exited with code ' + code);
}
});```

例子，检测退出代码：
```
var spawn = require('child_process').spawn,
child = spawn('bad_command');
child.stderr.on('data', function (data) {
if (/^execvp\(\)/.test(data.asciiSlice(0,data.length))) {
console.log('Failed to start child process.');
}
});```

请参见：child_process.exec()


**child_process.exec(command, [options], callback)**


使用子进程执行命令，缓存子进程的输出，并将子进程的输出以回调函数参数的形式返回。

```
var sys = require('sys'),
exec = require('child_process').exec,
child;
child = exec('cat *.js bad_file | wc -l',
function (error, stdout, stderr) {
sys.print('stdout: ' + stdout);
sys.print('stderr: ' + stderr);
if (error !== null) {
console.log('exec error: ' + error);
}
});```

回调函数得到的参数分别为(error, stdout, stderr)。成功时error 参数是null；失败时error 参数是Error 的实例。
error.code 是子进程的返回值，error.signal 保存终止进程的信号。
exec 函数的第二个可选参数可以用来指定一些选项。默认值为
```
{ encoding: 'utf8'
, timeout: 0
, maxBuffer: 200*1024
, killSignal: 'SIGKILL'
, cwd: null
, env: null
}```

如果timeout 为大于0的值，node 将终止运行时间超过timeout（单位为毫秒）的子进程。子进程将被终止信号
(killSignal，默认值为'SIGKILL')终止。maxBuffer 指定stdout 和stderr 最大可缓存数据的大小，如果超过这个值
子进程将被终止。


**child.kill(signal='SIGTERM')**


向子进程发送信号。如果不指定参数，默认发送'SIGTERM'信号。所有可用信号列表请参考signal(7)。
```
var spawn = require('child_process').spawn,
grep = spawn('grep', ['ssh']);
grep.on('exit', function (code, signal) {
console.log('child process terminated due to receipt of signal '+signal);
});
// send SIGHUP to process
grep.kill('SIGHUP');```

注意，虽然此函数名为kill，但发送的信号不一定终止子进程。kill 实际上只会向进程发送信号。
See kill(2)
更多信息请参考kill(2)

