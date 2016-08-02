### urllib.request的使用

#### urllib.request.urlopen

urllib.request模块提供了最基本的构造HTTP请求的方法，利用它可以模拟浏览器的一个请求发起过程，同时它还带有处理authenticaton\(授权验证\),（redirections）重定向,\(cookies\)浏览器Cookies以及其它内容。

好，那么首先我们来感受一下它的强大之处，以Python官网为例，我们来把这个网页抓下来。

```python
# coding=utf-8
import urllib.request

response = urllib.request.urlopen('https://www.python.org')
print(response.read().decode('utf-8'))
```

看一下运行结果。

![3-1-1](assets/3-1-1.png)

真正的代码只有两行，我们便完成了Python官网的抓取，输出了网页的源代码，得到了源代码之后呢？你想要的链接、图片地址、文本信息不就都可以提取出来了吗？

接下来我们看下它返回的到底是什么，利用`type`函数输出response的类型。

```python
# coding=utf-8
import urllib.request

response = urllib.request.urlopen('https://www.python.org')
print(type(response))
```

输出结果如下：

```
<class 'http.client.HTTPResponse'>

```

通过输出结果可以发现它是一个HTTPResposne类型的对象，它主要包含的方法有`read()`,`readinto()`,`getheader(name)`,`getheaders()`,`fileno()`等函数和`msg`,`version`,`status`,`reason`,`debuglevel`,`closed`等属性。
得到这个对象之后，赋值为response，然后就可以用response调用这些方法和属性，得到返回结果的一系列信息。

例如`response.read()`就可以得到返回的网页内容，`response.status`就可以得到返回结果的状态码，如200代表请求成功，404代表网页未找到等。

下面再来一个实例感受一下。

```
# coding=utf-8
import urllib.request

response = urllib.request.urlopen('https://www.python.org')
print(response.status)
print(response.getheaders())
print(response.getheader('Server'))
```

运行结果如下：

```
200
[('Server', 'nginx'), ('Content-Type', 'text/html; charset=utf-8'), ('X-Frame-Options', 'SAMEORIGIN'), ('X-Clacks-Overhead', 'GNU Terry Pratchett'), ('Content-Length', '47397'), ('Accept-Ranges', 'bytes'), ('Date', 'Mon, 01 Aug 2016 09:57:31 GMT'), ('Via', '1.1 varnish'), ('Age', '2473'), ('Connection', 'close'), ('X-Served-By', 'cache-lcy1125-LCY'), ('X-Cache', 'HIT'), ('X-Cache-Hits', '23'), ('Vary', 'Cookie'), ('Public-Key-Pins', 'max-age=600; includeSubDomains; pin-sha256="WoiWRyIOVNa9ihaBciRSC7XHjliYS9VwUGOIud4PB18="; pin-sha256="5C8kvU039KouVrl52D0eZSGf4Onjo4Khs8tmyTlV3nU="; pin-sha256="5C8kvU039KouVrl52D0eZSGf4Onjo4Khs8tmyTlV3nU="; pin-sha256="lCppFqbkrlJ3EcVFAkeip0+44VaoJUymbnOaEUk7tEU="; pin-sha256="TUDnr0MEoJ3of7+YliBMBVFB4/gJsv5zO7IxD9+YoWI="; pin-sha256="x4QzPSC810K5/cMjb05Qm4k3Bw5zBn4lTdO/nEW/Td4=";'), ('Strict-Transport-Security', 'max-age=63072000; includeSubDomains')]
nginx

```

可见，三个输出分别输出了响应的状态码，响应的头信息，以及通过传递一个参数获取了Server的类型。

利用以上最基本的urlopen方法，我们可以完成最基本的简单网页的GET请求抓取。

如果我们想给链接传递一些参数该怎么实现呢？我们首先看一下urlopen()函数的API。

```python
urllib.request.urlopen(url, data=None, [timeout, ]*, cafile=None, capath=None, cadefault=False, context=None)
```

可以发现除了第一个参数可以传递URL之外，我们还可以传递其它的内容，比如data（附加参数），timeout（超时时间）等等。

data参数是可选的，如果要添加data，它要是字节流编码格式的内容，即bytes类型，通过bytes函数可以进行转化，另外如果你传递了这个data参数，它的请求方式就不再是GET方式请求，而是POST。

下面用一个实例来感受一下。

```
# coding=utf-8
import urllib.parse
import urllib.request

data = bytes(urllib.parse.urlencode({'word': 'hello'}), encoding='utf8')
response = urllib.request.urlopen('http://httpbin.org/post', data=data)
print(response.read())

```
在这里我们传递了一个参数`word`，值是`hello`。它需要被转码成`bytes`（字节流）类型。其中转字节流采用了`bytes()`方法，第一个参数需要是`str`(字符串)类型，需要用`urllib.parse.urlencode()`方法来将参数字典转化为字符串。第二个参数指定编码格式，在这里指定为`utf8`。

提交的网址是`httpbin.org`，它可以提供http请求测试。`http://httpbin.org/post`这个地址可以用来测试POST请求，它可以输出请求和响应信息，其中就包含我们传递的data参数。


运行结果如下：

```
{
 "args": {},
 "data": "",
 "files": {},
 "form": {
 "word": "hello"
 },
 "headers": {
 "Accept-Encoding": "identity",
 "Content-Length": "10",
 "Content-Type": "application/x-www-form-urlencoded",
 "Host": "httpbin.org",
 "User-Agent": "Python-urllib/3.5"
 },
 "json": null,
 "origin": "123.124.23.253",
 "url": "http://httpbin.org/post"
}

```

我们传递的参数出现在了`form`中，这表明是模拟了表单提交的方式，以POST方式传输数据。












