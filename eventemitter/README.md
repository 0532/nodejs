# EventEmitter事件触发器
Node 中的很多对象都会触发事件，例如：一个TCP 服务器在收发每个数据流时都触发事件；子进程在退出时
会触发事件。所有能够触发事件的对象都是events.EventEmitter 的实例。


事件命名方式使用大小写分隔的风格。例如：'stream', 'data', 'messageBegin'。
可以将函数注册给对象，使其在事件触发时执行， 此类函数被称作‘监听器‘。

通过调用require('events').EventEmitter， 我们可以使用EventEmitter（事件触发器）类。
当向EventEmitters（事件触发器）对象上注册新的时间监听器时，都会触发'newListener'事件。
当事件触发器过程中出现错误时，典型的处理方式是它将触发一个'error'事件。Error 事件的特殊性在于：如果
没有函数处理这个事件，它将会输出调用堆栈，并随之退出应用程序。


**Event: 'newListener'**


    function (event, listener) { }


该事件在添加新监听器时被触发。


Event: 'error'


```
function (exception) { }```

 如果发生错误，'error'事件将会被触发。这是一个特殊事件，如果没有相应的监听函数监听这个事件， node 将
会结束应用程序的执行并显示异常堆栈。

**emitter.on(event, listener)**

向指定的事件监听器数组尾部添加一个新监听器。


```
server.on('stream', function (stream) {
console.log('someone connected!');
});```


**emitter.removeListener(event, listener)**

从指定监听器数组中删除一个监听器。需要注意的是，此操作将会改变处于被删监听器之后的那些监听器的索
引。

```
var callback = function(stream) {
console.log('someone connected!');
};
server.on('stream', callback);
// ...
server.removeListener('stream', callback);```

**emitter.removeAllListeners(event)**

删除指定事件的所有监听器。

**emitter.listeners(event)**

返回指定事件的监听器数组。你可以操作数组的内容，比如说删除一个监听器。

```
server.on('stream', function (stream) {
console.log('someone connected!');
});
console.log(sys.inspect(server.listeners('stream'));
// [ [Function] ]```


```
emitter.emit(event, [arg1], [arg2], [...])```

使用所提供的参数，依次执行事件监听器数组中的每一个监听函数。


