# Query String 查询字串
此模块能处理查询字串(query strings)，提供以下方法：


**querystring.stringify(obj, sep='&', eq='=', munge=true)**


序列化对象至查询字串。选择性地覆写默认分割符和增补字符(assignment characters)。
例子：
```
querystring.stringify({foo: 'bar'})
// returns
'foo=bar'
querystring.stringify({foo: 'bar', baz: 'bob'}, ';', ':')
// returns
'foo:bar;baz:bob'```

此方法默认由阵列和对象(obj)排列成PHP/Rails 风格的查询字串，例子：


```
querystring.stringify({foo: ['bar', 'baz', 'boz']})
// returns
'foo%5B%5D=bar&foo%5B%5D=baz&foo%5B%5D=boz'
querystring.stringify({foo: {bar: 'baz'}})
// returns
'foo%5Bbar%5D=baz'```

若希望停用字元解析(例如当生成参数予Java servlet 时)，可以设置munge(字元解析) 参数成false(假值)，例
子：
```
querystring.stringify({foo: ['bar', 'baz', 'boz']}, '&', '=', false)
// returns
'foo=bar&foo=baz&foo=boz'```

注意当munge(字元解析) 为false 时，参数名称仍会被解析。

**querystring.parse(str, sep='&', eq='=')**


反序列化查询字串至对象。选择性地覆写默认分割符和增补字符
(assignment characters)。
```
querystring.parse('a=b&b=c')
// returns
{ 'a': 'b'
, 'b': 'c'
}```


这方法可以解析已解析和未解析的查询字串。


**querystring.escape**


querystring.stringify 中所使用的escape 方法。您可以覆写它。


**querystring.unescape**


querystring.parse 中所使用的unescape 方法。您可以覆写它。
