# Synopsis 总述
使用node 实现的web 服务器示例，它返回'Hello World'：
```
var http = require('http');
http.createServer(function (request, response) {
response.writeHead(200, {'Content-Type': 'text/plain'});
response.end('Hello World\n');
}).listen(8124);
console.log('Server running at http://127.0.0.1:8124/');```

将上述代码保存为example.js，并使用如下命令执行这个服务程序：
```
> node example.js
Server running at http://127.0.0.1:8124/```

文档中的其他示例，均可以类似的方式执行。
