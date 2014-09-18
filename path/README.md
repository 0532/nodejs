# Path模块
此模块包含很多用于处理文件路径的小工具。你可以通过require('path')使用该模块。它提供了如下函数：


**path.join([path1], [path2], [...])**


将所有参数连接在一起并解析生成新的路径。
示例：


```
node> require('path').join(
... '/foo', 'bar', 'baz/asdf', 'quux', '..')
'/foo/bar/baz/asdf'```

**path.normalizeArray(arr)**


转化路径的各部分，将'..'和'.'替换为实际的路径。
示例：


```
path.normalizeArray([,
'foo', 'bar', 'baz', 'asdf', 'quux', '..'])
// returns
[ , 'foo', 'bar', 'baz', 'asdf' ]```

**path.normalize(p)**

转化路径字符串，将'..'和'.'替换为实际的路径。
示例：


```
path.normalize('/foo/bar/baz/asdf/quux/..')
// returns
'/foo/bar/baz/asdf'```

**path.dirname(p)**


返回路径中代表文件夹的部分，同Unix 的dirname 命令类似。
示例：
```
path.dirname('/foo/bar/baz/asdf/quux')
// returns
'/foo/bar/baz/asdf'```

**path.basename(p, [ext])**


返回路径中的最后一部分。同Unix 命令bashname 类似。
示例“
```
path.basename('/foo/bar/baz/asdf/quux.html')
// returns
'quux.html'
path.basename('/foo/bar/baz/asdf/quux.html', '.html')
// returns
'quux'```

**path.extname(p)**


返回路径中文件的后缀名，即路径中最后一个'.'之后的部分。如果一个路径中并不包含'.'或该路径只包含一个'.'
且这个'.'为路径的第一个字符，则此命令返回空字符串。如下所示：


```
path.extname('index.html')
// returns
'.html'
path.extname('index')
// returns```

**path.exists(p, [callback])**


检测给定的文件路径是否存在。然后传递结果(true 或false)给回调函数。


```
path.exists('/etc/passwd', function (exists) {
sys.debug(exists ? "it's there" : "no passwd!");
});```

