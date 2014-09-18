# DNS 域名解析
（译著，Windows 版本的NodeJS 暂时没有实现DNS 功能）
使用require('dns')来访问这个模块。


下面是一个先解析'www.google.com'，然后将解析出来的IP 地址再做反向解析。
```
var dns = require('dns');
dns.resolve4('www.google.com', function (err, addresses) {
if (err) throw err;
console.log('addresses: ' + JSON.stringify(addresses));
addresses.forEach(function (a) {
dns.reverse(a, function (err, domains) {
if (err) {
console.log('reverse for ' + a + ' failed: ' +
err.message);
} else {
console.log('reverse for ' + a + ': ' +
JSON.stringify(domains));
}
});
});```
**dns.lookup(domain, family=null, callback)**

将一个域名(例如. 'google.com')解析成为找到的第一个A(IPv4)或者AAAA(IPv6)记录。
回调函数的有(err, address, family)这三个参数。address 参数是一个代表IPv4或IPv6的地址的字符串。family 是
一个表示地址版本的整形数字4或6(并不一定是解析域名时传递的数字)。


**dns.resolve(domain, rrtype='A', callback)**


将参数domain(比如'google.com')按照参数rrtype 所指定数据类型解析到一个数组中。合法的类型为A（IPV4地
址），AAAA（IPV6地址），MX（mail exchange records）,TXT(text records)，SRV（SRV records），和PTR（used
for reveres IP lookups）。


回调函数（callback）接受两个参数：err 和address。参数address 中的每一项根据记录类型(record type)分割，在
下面lookup 方法的文档里有详细的解释。
当有错误发生时，参数err 的内容是一个Error 对象的实例，err 的errno 属性是下面错误代码列表中的一个，err
的message 属性是一个用英语表述的错误解释。


**dns.resolve4(domain, callback)**


与dns.resolve()类似,但是仅对IPV4地址进行查询(A records)。addresses
是一个IPV4地址数组(例如['74.125.79.104',
'74.125.79.105', '74.125.79.106'])


**dns.resolve6(domain, callback)**


除了这个函数是对IPV6地址的查询外与dns.resolve4()很类似(一个AAAA 查询)。


**dns.resolveMx(domain, callback)**


与dns.resolve()很类似.但是仅做mail exchange 查询(MX 类型记录)。
回调函数的参数address 是一个MX 类型记录的数组,每个记录有一个优先级属性和一个交换属性(类似[{'priority':
10, 'exchange': 'mx.example.com'},...])


**dns.resolveTxt(domain, callback)**


与dns.resolve()很相似,但是仅可以进行text 查询(TXT 记录).地址是一个对于域来说有效的text 记录数组（类似
['v=spf1 ip4:0.0.0.0 ~all']）


**dns.resolveSrv(domain, callback)**


与dns.resolve()很类似,但仅是只查询service 记录(srv records)。地址是一个对于域来说有效的SRV 记录的数组，
SRV 记录的属性有优先级、权重、端口， 名字( 例如[{'priority': 10, {'weight': 5, 'port': 21223, 'name':'service.example.com'}, ...])


**dns.reverse(ip, callback)**


反向解析一个IP 地址到一个域名数组。
callback 参数有两个参数(err,domains)。
如果发生了错误,err 为Error 对象的实例。
每个DNS 查询可以返回如下错误代码：


1 超时、返回SERVFAIL 或者类似的错误


2 返回内容里有乱码



3 域名不存在


4 域名存在但是没有所请求的查询类型的数据


5��� 处理过程中内存溢出


6��� 查询语句异常
