# Addons 扩展
扩展是动态链接的共享对象，可以与C 和C++ 库链合。目前API 是相当复杂，涉及数个库的知识：
* V8 JavaScript, C++ 库。能在C++中与JavaScript 链合：创建对象，调用函数等。文档大部份存放於v8.h 的
标头文件(deps/v8/include/v8.h)。


 * libev, C 语言事件循环库。（提供一个能当文档描述符有特定事件发生，或等待时间超过时，执行回调函数
的机制。) 当I/O 执行时， 需要使用libev 。Node 利用EV_DEFAULT 事件循环。文档存放於
http://cvs.schmorp.de/libev/ev.html。

* libeio，C 语言执行绪集区库。能使POSIX 系统异步执行。由於通常已封装於src/file.cc ，所以毋必要
使用。若需使用，查阅标头文件deps/libeio/eio.h。


* 内部Node 库，最主要的是node::ObjectWrap 类，经常用作参考。


* 其他，查阅deps/。


Node 静态编译所有组件成可执行文件。当您编译您的模块时，您不必考虑以上库的连结。
制作一个小型扩展能达到以下效用(C++ 除外)：


exports.hello = 'world';
创建文件hello.cc：
```
#include <v8.h>
using namespace v8;
extern "C" void
init (Handle<Object> target)
{
HandleScope scope;
target->Set(String::New("hello"), String::New("World"));
}```

此源文件需要编译成hello.node（二进制扩展）。需要创建一个
python 文件wscript：
```
srcdir = '.'
blddir = 'build'
VERSION = '0.0.1'
def set_options(opt):
opt.tool_options('compiler_cxx')
def configure(conf):
conf.check_tool('compiler_cxx')
conf.check_tool('node_addon')
def build(bld):
obj = bld.new_task_gen('cxx', 'shlib', 'node_addon')
obj.target = 'hello'
obj.source = 'hello.cc'```


执行node-waf configure build 将会创建您的扩展文件至build/default/hello.node。
node-waf 是http://code.google.com/p/waf/[WAF]，基於python 的编译系统。node-waf 为使用者提供轻易。
所有Node 扩展必须输出一函数init ，并包含此声明：
extern 'C' void init (Handle<Object> target)


至现时为止，此乃完整的扩展说明文件。请阅http://github.com/ry/node_postgres 以取得真实范例。
