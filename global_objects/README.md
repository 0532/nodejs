# Global Objects 全局对象
这些对象在全局范围内都可用，并且可以从任何位置访问。

**global**

全局命名空间对象


**process**


有关process 对象请参见'process'章节。


**require()**


To require modules. See the 'Modules' section.
用来加载模块。参见“Modules 模块”这一节。


**require.paths**


一个保存了require 函数搜索路径的数组。你可以修改此数组添加自定义路径。


例子：在搜索路径列表开头添加一个路径。


```
require.paths.unshift('/usr/local/node');
console.log(require.paths);
// /usr/local/node,/Users/mjr/.node_libraries```

**__filename**


当前正在执行的脚本的文件名。此为绝对路径，且和命令行参数所指定的文件名不一定相同。


例子：执行/Users/mjr 下的example.js 文件。


```
console.log(__filename);
// /Users/mjr/example.js```

**__dirname**


当前执行脚本的文件夹。


例子：执行/Users/mid 下的example.js 文件。


```
console.log(__dirname);
// /Users/mjr```

**module**


指向当前模块的引用。特别指出，module.exports 就是exprots 对象。更多信息请参看源代码文件src/process.js。



