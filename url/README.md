# URL模块
此模块用于解析URL，你可以通过require('url')来使用它。


由于各URL 不尽相同，经过解析的URL 对象有如下部分或者全部的域。任何URL 中不包含的域将不会出现在
解析后的URL 对象中。如下所示：
'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'


*href*


原始的URL。例如：'http://user:pass@host.com:8080/p/a/t/h?query=string#hash'


*protocol*

请求的协议。例如：'http'

*host*

URL 中主机地址部分，包括端口和验证信息。例如：'user:pass@host.com:8080'


*auth*

URL 中的验证信息。例如：'user:pass'


*hostname*

仅仅包括主机地址。例如：'host.com'

*port*

主机的端口号。例如：'8080'

*pathname*

URL 中的路径(path)部分，即紧跟在主机地址之后查询参数之前的部分，包括路径开头的斜线。例如：'/p/a/t/h'

*search*


URL 中的参数部分，包括开头的问号。例如：?query=string


*query*


查询字符串中的参数部分或者是解析过的查询字串对象（译注：根据解析URL 时设置的参数不同，此属性的内
容也不同）。例如：'query=string' 或者{'query':'string'}


*hash*


URL 中的锚记部分，包括开头的井号。例如：'#hash'
URL 模块还提供如下方法：

**url.parse(urlStr, parseQueryString=false)**

此函数接受一个URL 字符串并返回一个对象。如果第二个参数传递true，node 会使用querystring 模块解析查询
字符串。


**url.format(urlObj)**


此函数接受一个URL 对象，并返回一个格式化后的URL 字符串。


**url.resolve(from, to)**


此函数接受一个base URL 和一个href URL，并像浏览器解析锚记一样解析它们。
