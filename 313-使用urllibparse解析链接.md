### 使用urllib.parse解析链接

这个模块定义了处理`URL`的标准接口，例如实现`URL`各部分的抽取，合并以及链接转换。它支持如下类型的链接处理：`file`、`ftp`、`gopher`、`hdl`、`http`、`https`、`imap`、`mailto`、 `mms`、`news`、`nntp`、`prospero`、`rsync`、`rtsp`、`rtspu`、`sftp`、`shttp`、 `sip`、`sips`、`snews`、`svn`、`svn+ssh`、`telnet`、`wais`。

#### urllib.parse.urlparse()的用法

常用的函数有`urllib.parse.urlparse()`。

先用一个实例来感受一下：

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

它有三个参数：

第一个参数`urlstring`是必填项，即待解析的`URL`。

第二个参数`scheme`是默认的协议（比如`http`、`https`等），假如这个链接没有带协议信息，会将这个作为默认的协议。

我们用一个实例感受一下：

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

下面我们用一个实例感受一下：

```python
# coding=utf-8
from urllib.parse import urlparse

result = urlparse('http://www.baidu.com/index.html;user?id=5#comment', allow_fragments=False)
print(result)
```

运行结果：

```python
ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html', params='user', query='id=5#comment', fragment='')

```

假设链接中不包含`parameters`和`query`呢？

再来一个实例：

```python
# coding=utf-8
from urllib.parse import urlparse

result = urlparse('http://www.baidu.com/index.html#comment', allow_fragments=False)
print(result)
```

运行结果：

```python
ParseResult(scheme='http', netloc='www.baidu.com', path='/index.html#comment', params='', query='', fragment='')

```

可以发现当链接中不包含`params`和`query`时，`fragment`便会被解析为`path`的一部分。

返回结果`ParseResult`实际上是一个元组，你可以用索引顺序来获取，也可以用属性名称获取。

```python
# coding=utf-8
from urllib.parse import urlparse

result = urlparse('http://www.baidu.com/index.html#comment', allow_fragments=False)
print(result.scheme, result[0], result.netloc, result[1], sep='\n')
```

在这里我们分别用索引和属性名获取了`scheme`和`netloc`，运行结果如下：

```
http
http
www.baidu.com
www.baidu.com
```

可以发现二者结果是一致的，两种方法都可以成功获取。

#### urllib.parse.urlunparse()的用法

有了`urlparse()`那相应地就有了它的对立方法`urlunparse()`。

接受的参数是一个可迭代对象，但是它的长度必须是6，否则会抛出参数数量不足或者过多的问题。

先用一个实例感受一下：

```python
# coding=utf-8
from urllib.parse import urlunparse

data = ['http', 'www.baidu.com', 'index.html', 'user', 'a=6', 'comment']
print(urlunparse(data))

```

参数用了`list`类型，当然你也可以用其他的类型如`tuple`或者特定的数据结构。

运行结果如下：

```
http://www.baidu.com/index.html;user?a=6#comment
```

#### urllib.parse.urlsplit()的用法

这个和`urlparse()`方法非常相似，只不过它不会单独解析`parameters`这一部分，只返回五个结果。上面例子中的`parameters`会合并到`path`中。

用一个实例感受一下：

```python
# coding=utf-8
from urllib.parse import urlsplit

result = urlsplit('http://www.baidu.com/index.html;user?id=5#comment')
print(result)
```
运行结果：

```
SplitResult(scheme='http', netloc='www.baidu.com', path='/index.html;user', query='id=5', fragment='comment')
```

返回结果是`SplitResult`，其实也是一个元组类型，可以用属性获取值也可以用索引来获取。

```python
# coding=utf-8
from urllib.parse import urlsplit

result = urlsplit('http://www.baidu.com/index.html;user?id=5#comment')
print(result.scheme, result[0])
```

运行结果：

```
http http
```







