# REPL 交互执行


node 的“读入、运行、输出循环模式(REPL)”既可以单独执行也很容易嵌入其它程序中。REPL 提供了一种交互
式执行Javascript 并查看结果的模式。这种模式可以用来调试、测试或者仅仅用来某些新特性。


如果直接执行node 而不跟任何参数就会进入REPL 模式。它类似于简化的emacs 行编辑模式。

```
mjr:~$ node
Type '.help' for options.
node> a = [ 1, 2, 3];
[ 1, 2, 3 ]
node> a.forEach(function (v) {
... console.log(v);
... });
1
2
3```

要使用高级行编辑功能，设置环境变量NODE_NO_READLINE=1并执行node。这样REPL 就会使用标准终端
设置，如此一来你就可以使用rlwarp 来执行高级行编辑。
示例，你可以在bashrc 文件中添加如下指令：
```
alias node="env NODE_NO_READLINE=1 rlwrap node"
repl.start(prompt='node> ', stream=process.openStdin())```

REPL 执行时将使用prompt 参数的值作为输入提示符，并使用stream 参数执行所有I/O 操作。prompt 为可选
参数，默认值为'node>', stream 为可选参数，默认值为process.openStdin()；


同一个node 进程可以启动多个REPL，每个REPL 将会共享部分全局对象，但是它们都有自己唯一的I/O。
示例，分别使用标准输出（控制台）、Unix Socket 和TCP Socket 启动REPL：
```
var net = require("net"),
repl = require("repl");
connections = 0;
repl.start("node via stdin> ");
net.createServer(function (socket) {
connections += 1;
repl.start("node via Unix socket> ", socket);
}).listen("/tmp/node-repl-sock");
net.createServer(function (socket) {
connections += 1;
repl.start("node via TCP socket> ", socket);
}).listen(5001);```

在控制台执行上述程序将使用标准输入（当前控制台）启动REPL，同时其他REPL 客户端可以通过Unix socket
或者TCP socket 连接。你可以使用telnet 连接到TCP socket，用socat 连接到Unix 或TCP sockets。
不使用标准输入（控制台）而是用Unix socket 服务启动REPL，可以让你轻易连接到一个长时间运行的node 进
程而不用重新启动该进程。


**REPL Features REPL支持的特性**


在REPL 执行时，可以输入Control+D 退出。你也可以输入跨越多行的表达式。
特殊标量'_'（下划线）保存了上一个表达式执行后的值。
```
node> [ "a", "b", "c" ]
[ 'a', 'b', 'c' ]
node> _.length
3
node> _ += 1
4```


REPL 提供了访问全局作用域内任何变量的能力，你也可以通过将变量赋值给REPL 的context 对象来向REPL
暴露该变量。例如：


```
// repl_test.js
var repl = require("repl"),
msg = "message";
repl.start().context.m = msg;
对于REPL 来说，context 对象中的值就犹如在本地作用域内：
mjr:~$ node repl_test.js
node> m
'message'```

如下是一些REPL 命令：


.break - 当想要放弃当前输入的多行命令时，可以使用.break 命令重新开始输入。

.clear - 将context 重置为空对象并清空（当前正在输入的）多行表达式。


.exit - 管理I/O 流，此操作将关闭REPL。


.help - 显示特殊命令的帮助。
