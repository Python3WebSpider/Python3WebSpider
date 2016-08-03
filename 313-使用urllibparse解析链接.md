### 使用urllib.parse解析链接

这个模块定义了处理`URL`的标准接口，例如实现`URL`各部分的抽取，合并以及链接转换。它支持如下类型的链接处理：`file`、`ftp`、`gopher`、`hdl`、`http`、`https`、`imap`、`mailto`、 `mms`、`news`、`nntp`、`prospero`、`rsync`、`rtsp`、`rtspu`、`sftp`、`shttp`、 `sip`、`sips`、`snews`、`svn`、`svn+ssh`、`telnet`、`wais`。

常用的函数有`urllib.parse.urlparse()`。

先用一个实例来感受一下。

```python
# coding=utf-8
from urllib.parse import urlparse

result = urlparse('http://www.baidu.com/index.html;user?id=5#comment')
print(type(result), result)
```
在这里我们首先输出了结果的类型，然后将结果也输出出来。

运行结果：

```python
<class 'urllib.parse.ParseResult'> ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html', params='user', query='id=5', fragment='comment')

```

观察可以看到，返回结果是一个`ParseResult`类型的对象，它包含了六个部分，分别是`scheme`、`netloc`、`path`、`params`、`query`、`fragment`。

观察一下实例的网址。

```
http://www.baidu.com/index.html;user?id=5#comment
```

`urlparse()`方法将其拆分成了六部分，大体观察可以发现，解析时有特定的分隔符，比如`://`前面的就是`scheme`，第一个`/`前面便是`netloc`，分号`;`前面是`params`等等。

所以可以得出一个标准的链接格式如下：

```
scheme://netloc/path;parameters?query#fragment
```

除了这种最基本的解析方式，`urlopen()`方法还有其他配置吗？接下来看一下它的`API`。

```python
urllib.parse.urlparse(urlstring, scheme='', allow_fragments=True)
```

它有三个参数。

第一个参数`urlstring`是必填项，即待解析的`URL`。

第二个参数`scheme`是默认的协议（比如`http`、`https`等），假如这个链接没有带协议信息，会将这个作为默认的协议。

我们用一个实例感受一下。

```python
# coding=utf-8
from urllib.parse import urlparse

result = urlparse('www.baidu.com/index.html;user?id=5#comment', scheme='https')
print(result)
```

运行结果：

```python
ParseResult(scheme='https', netloc='', path='www.baidu.com/index.html', params='user', query='id=5', fragment='comment')

```
可以发现，我们提供的链接没有包含最前面的`scheme`，但是通过指定默认的`scheme`参数，返回的结果带有`https`。

假设我们带上了`scheme`呢？

```python
result = urlparse('http://www.baidu.com/index.html;user?id=5#comment', scheme='https')

```

结果如下：

```python
ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html', params='user', query='id=5', fragment='comment')

```

可见`scheme`参数只有在链接中不包含`scheme`信息时才会生效，如果链接中有了`scheme`，那就返回解析出的`scheme`。

第三个参数`allow_fragments`是是否忽略`fragment`，如果它被设置为`False`，`fragment`部分就会被忽略，它会被解析为`path`、`parameters`或者`query`的一部分，`fragment`部分为空。

返回结果是一个



































