# Modules 模块
Node 使用CommonJS 的模块系统。
Node 同时也拥有一个简单的模块加载系统。在Node 的世界里，文件和模块是一一对应的，比如，以下的程序
foo.js 会向大家演示加载同一目录下的circle.js 模块。


The contents of foo.js:
foo.js 的代码：


```
var circle = require('./circle');
console.log( 'The area of a circle of radius 4 is '
+ circle.area(4));```

The contents of circle.js:
cirle.js 的代码：


```
var PI = 3.14;
exports.area = function (r) {
return PI * r * r;
};
exports.circumference = function (r) {
return 2 * PI * r;
};```

模块circle.js 有两个方法area()和circumference()。为了使其对外可见，将其导出到一个特殊的对象exprots（还
可以用this 来替代exports)。此模块中的本地变量是私有的。在这个例子中，PI 便是circle 模块的似有变量，puts()
方法则是引用自系统自带模块'sys'。没有'./'前缀的模块都是写内建的。我们将会详细讲解这个特性。


使用'./'前缀加载的模块必须和加载模块的文件位于同一文件夹下，所以将circle.js 和foo.js 放在同一目录下。
如果不用'./'前缀，比如require('assert')，那么则会在require.paths 数组指定的路径下寻找，require.paths 在我的机
器上输出如下：

[ '/home/ryan/.node_libraries' ] [ '/home/ryan/.node_libraries' ]
所以当呼叫require('assert')是，系统会沿着如下路径寻找该模块：
* 1: /home/ryan/.node_libraries/assert.js
* 2: /home/ryan/.node_libraries/assert.node
* 3: /home/ryan/.node_libraries/assert/index.js
* 4: /home/ryan/.node_libraries/assert/index.node


有'.node'后缀的模块是Node 系统的二进制模块:可以参考'Addons'来得到更多信息。'index.js'允许我们把一个模块
作为一个目录打包。


require.paths 可以在运行时修改或者在类UNIX 系统下通过修改NODE_PATH 环境变量来达到同样的目的。
