### requests的基本使用

#### 实例引入

在`urllib`库中，有`urllib.request.urlopen(url)`的方法，实际上它是以`GET`方式请求了一个网页。

那么在`requests`中，相应的方法就是`requests.get(url)`，是不是感觉表达更明确一些？

下面我们用一个实例来感受一下：

```python
# coding=utf-8
import requests

r = requests.get('https://www.baidu.com/')
print(type(r))
print(r.status_code)
print(type(r.text))
print(r.text)
print(r.cookies)
```

运行结果如下：

```python
<class 'requests.models.Response'>
200
<class 'str'>
<html>
<head>
	<script>
		location.replace(location.href.replace("https://","http://"));
	</script>
</head>
<body>
	<noscript><meta http-equiv="refresh" content="0;url=http://www.baidu.com/"></noscript>
</body>
</html>
<RequestsCookieJar[<Cookie BIDUPSID=992C3B26F4C4D09505C5E959D5FBC005 for .baidu.com/>, <Cookie PSTM=1472227535 for .baidu.com/>, <Cookie __bsi=15304754498609545148_00_40_N_N_2_0303_C02F_N_N_N_0 for .www.baidu.com/>, <Cookie BD_NOT_HTTPS=1 for www.baidu.com/>]>

```
上面的例子分别输出了请求响应的类型，状态码，响应体内容的类型，响应体内容还有Cookies。

通过上述实例可以发现，它的返回类型是`requests.models.Response`，响应体的类型是字符串`str`，`Cookies`的类型是`RequestsCookieJar`。

我们可以发现，使用了`requests.get(url)`方法就成功实现了一个`GET`请求。这倒不算什么，更方便的在于其他的请求类型依然可以用一句话来完成。

用一个实例来感受一下：

```python
r = requests.post('http://httpbin.org/post')
r = requests.put('http://httpbin.org/put')
r = requests.delete('http://httpbin.org/delete')
r = requests.head('http://httpbin.org/get')
r = requests.options('http://httpbin.org/get')
```

怎么样？是不是比`urllib`简单太多了？

其实这只是冰山一角，更多的还在后面呢。

#### GET请求

HTTP中最常见的请求之一就是`GET`请求，我们首先来详细了解下利用`requests`来构建`GET`请求的方法以及相关属性方法操作。

首先让我们来构建一个最简单的`GET`请求，请求`httpbin.org/get`，它会判断如果你是`GET`请求的话，会返回响应的请求信息。

```python
# coding=utf-8
import requests

r = requests.get('http://httpbin.org/get')
print(r.text)
```

运行结果如下：

```json
{
  "args": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.10.0"
  }, 
  "origin": "122.4.215.33", 
  "url": "http://httpbin.org/get"
}
```

可以发现我们成功发起了get请求，请求的链接和头信息都有相应的返回。

那么`GET`请求，如果要附加额外的信息一般是怎样来添加？没错，那就是直接当做参数添加到`url`后面。

比如现在我想添加两个参数，名字name是germey，年龄age是22。构造这个请求链接是不是我们要直接写成`r = requests.get("http://httpbin.org/get?name=germey&age=22")`？

可以是可以，但是不觉得很不人性化吗？一般的这种信息数据我们会用字典`{"name": "germey", "age": 22}`来存储，那么怎样来构造这个链接呢？

同样很简单，利用`params`这个参数就好了。

实例如下：

```python
# coding=utf-8
import requests

data = {
    'name': 'germey',
    'age': 22
}
r = requests.get("http://httpbin.org/get", params=data)
print(r.text)
```

运行结果如下：

```
{
  "args": {
    "age": "22", 
    "name": "germey"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.10.0"
  }, 
  "origin": "122.4.215.33", 
  "url": "http://httpbin.org/get?age=22&name=germey"
}

```

通过返回信息我们可以判断，请求的链接自动被构造成了`http://httpbin.org/get?age=22&name=germey`，是不是很方便？

另外，网页的返回类型实际上是`str`类型，但是它很特殊，是`Json`的格式，所以如果我们想直接把返回结果解析，得到一个字典`dict`格式的话，可以直接调用`json()`方法。

用一个实例来感受一下：

```python
# coding=utf-8
import requests

r = requests.get("http://httpbin.org/get")
print(type(r.text))
print(r.json())
print(type(r.json()))
```

运行结果如下：

```
<class 'str'>
{'headers': {'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Host': 'httpbin.org', 'User-Agent': 'python-requests/2.10.0'}, 'url': 'http://httpbin.org/get', 'args': {}, 'origin': '182.33.248.131'}
<class 'dict'>
```

可以发现，调用`json()`方法，就可以将返回结果是`Json`格式的字符串转化为字典`dict`。

但注意，如果返回结果不是`Json`格式，便会出现解析错误，抛出`json.decoder.JSONDecodeError`的异常。

#### 抓取网页

如上的请求链接返回的是`Json`形式的字符串，那么如果我们请求普通的网页，那么肯定就能获得相应的内容了。

下面我们以知乎－发现页面为例来体验一下：

```python
# coding=utf-8
import requests
import re

headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36'
}
r = requests.get("https://www.zhihu.com/explore", headers=headers)
pattern = re.compile('explore-feed.*?question_link.*?>(.*?)</a>', re.S)
titles = re.findall(pattern, r.text)
print(titles)
```

如上代码，我们请求了知乎－发现页面`https://www.zhihu.com/explore`，在这里加入了头信息，头信息中包含了`User-Agent`信息，也就是浏览器标识信息。如果不加这个，知乎会禁止抓取。

在接下来用到了最基础的正则表达式，来匹配出所有的问题内容，关于正则表达式会在后面的章节中详细介绍，在这里作为用到实例来配合讲解。

运行结果如下：

```
['\n为什么很多人喜欢提及「拉丁语系」这个词？\n', '\n在没有水的情况下水系宝可梦如何战斗？\n', '\n有哪些经验可以送给 Kindle 新人？\n', '\n谷歌的广告业务是如何赚钱的？\n', '\n程序员该学习什么，能在上学期间挣钱？\n', '\n有哪些原本只是一个小消息，但回看发现是个惊天大新闻的例子？\n', '\n如何评价今敏？\n', '\n源氏是怎么把那么长的刀从背后拔出来的？\n', '\n年轻时得了绝症或大病是怎样的感受？\n', '\n年轻时得了绝症或大病是怎样的感受？\n']

```

发现成功提取出了所有的问题内容。

没错，提取信息就是这么方便。

#### 抓取二进制数据

在上面的例子中，我们抓取的是知乎的一个页面，实际上它返回的是一个`HTML`文档，那么如果我们想抓去图片、音频、视频等文件的话应该怎么办呢？

我们都知道，图片、音频、视频这些文件都是本质上由二进制码组成的，由于有特定的保存格式和对应的解析方式，我们才可以看到这些形形色色的多媒体。所以想要抓取他们，那就需要拿到他们的二进制码。

下面我们以GitHub的站点图标为例来感受一下：

```python
# coding=utf-8
import requests

r = requests.get("https://github.com/favicon.ico")
print(r.text)
print(r.content)
```

抓取的内容是站点徽标，也就是在浏览器每一个标签上显示的小图标。

![](/assets/3-2-1.png)

在这里打印了`response`的两个属性，一个是`text`，另一个是`content`。

运行结果如下，由于包含特殊内容，在此放运行结果的图片：

![](/assets/3-2-2.png)

那么前两行便是`r.text`的结果，最后一行是`r.content`的结果。

可以注意到，前者出现了乱码，后者结果前面带有一个`b`，代表这是`bytes`类型的数据。由于图片是二进制数据，所以前者在打印时转化为`str`类型，也就是图片直接转化为字符串，理所当然会出现乱码。

两个属性有什么区别？前者返回的是字符串类型，如果返回结果是文本文件，那么用这种方式直接获取其内容即可。如果返回结果是图片、音频、视频等文件，`requests`会为我们自动解码成`bytes`类型，即获取字节流数据。

进一步地，我们可以将刚才提取到的图片保存下来。

```python
# coding=utf-8
import requests

r = requests.get("https://github.com/favicon.ico")
with open('favicon.ico', 'wb') as f:
    f.write(r.content)
    f.close()
```

在这里用了`open()`函数，第一个参数是文件名称，第二个参数代表以二进制写的形式打开，可以向文件里写入二进制数据，然后保存。

运行结束之后，可以发现在文件夹中出现了名为`favicon.ico`的图标。

![](/assets/3-2-3.ico)

同样的，音频、视频文件也可以用这种方法获取。

#### 添加头信息

如`urllib.request`一样，我们也可以通过`headers`参数来传递头信息。

比如上面的知乎的例子，如果不传递头信息，就不能正常请求：

```python
# coding=utf-8
import requests

r = requests.get("https://www.zhihu.com/explore")
print(r.text)
```

运行结果如下：

```html
<html><body><h1>500 Server Error</h1>
An internal server error occured.
</body></html>
```

但如果加上请求头信息，那就没问题了：

```python
# coding=utf-8
import requests

headers = {
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/52.0.2743.116 Safari/537.36'
}
r = requests.get("https://www.zhihu.com/explore", headers=headers)
print(r.text)
```

当然你可以在`headers`这个数组中任意添加其他的头信息。

#### 基本POST请求

在前面我们讲解了最基本的`GET`请求，另外一种比较常见的请求方式就是`POST`了，就像模拟表单提交一样，将一些数据提交到某个链接。

使用`requests`实现`POST`请求同样非常简单。

我们先用一个实例来感受一下：

```python
# coding=utf-8
import requests

data = {'name': 'germey', 'age': '22'}
r = requests.post("http://httpbin.org/post", data=data)
print(r.text)
```

上面的例子请求的是`httpbin.org/post`，它可以判断如果请求是`POST`方式，就把相关请求信息输出出来。

运行结果如下：

```
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "age": "22", 
    "name": "germey"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "18", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.10.0"
  }, 
  "json": null, 
  "origin": "182.33.248.131", 
  "url": "http://httpbin.org/post"
}

```

可以发现，成功获得了返回结果，返回结果中的`form`部分就是提交的数据，那么这就证明`POST`请求成功发送了。

#### 响应

发送请求之后，得到的自然就是响应，在上面的实例中我们使用了`text`和`content`获取了响应内容。不过还有很多属性和方法可以获取其他的信息。

比如响应状态码、响应头、Cookies。

下面用一个实例来感受一下：

```python
# coding=utf-8
import requests

r = requests.get('http://www.jianshu.com')
print(type(r.status_code), r.status_code)
print(type(r.headers), r.headers)
print(type(r.cookies), r.cookies)
print(type(r.url), r.url)
print(type(r.history), r.history)
```

在这里分别打印输出了响应状态吗`status_code`，响应头`headers`，Cookies，请求连接，请求历史的类型和内容。

运行结果如下：

```
<class 'int'> 200
<class 'requests.structures.CaseInsensitiveDict'> {'X-Runtime': '0.006363', 'Connection': 'keep-alive', 'Content-Type': 'text/html; charset=utf-8', 'X-Content-Type-Options': 'nosniff', 'Date': 'Sat, 27 Aug 2016 17:18:51 GMT', 'Server': 'nginx', 'X-Frame-Options': 'DENY', 'Content-Encoding': 'gzip', 'Vary': 'Accept-Encoding', 'ETag': 'W/"3abda885e0e123bfde06d9b61e696159"', 'X-XSS-Protection': '1; mode=block', 'X-Request-Id': 'a8a3c4d5-f660-422f-8df9-49719dd9b5d4', 'Transfer-Encoding': 'chunked', 'Set-Cookie': 'read_mode=day; path=/, default_font=font2; path=/, _session_id=xxx; path=/; HttpOnly', 'Cache-Control': 'max-age=0, private, must-revalidate'}
<class 'requests.cookies.RequestsCookieJar'> <RequestsCookieJar[<Cookie _session_id=xxx for www.jianshu.com/>, <Cookie default_font=font2 for www.jianshu.com/>, <Cookie read_mode=day for www.jianshu.com/>]>
<class 'str'> http://www.jianshu.com/
<class 'list'> []

```

`session_id`过长在此简写。可以看到，`headers`还有`cookies`这两个部分都是特定的数据结构，打开浏览器同样可以发现有同样的响应头信息。

##### 状态码

在这里状态码常用来判断请求是否成功，`requests`还提供了一个内置的状态码查询对象`requests.codes`。比如你可以通过`if r.status_code == requests.codes.ok`来判断请求是否成功。

用一个实例来感受一下：

```python
# coding=utf-8
import requests

r = requests.get('http://www.jianshu.com')
exit() if not r.status_code == requests.codes.ok else print('Request Successfully')
```

在这里，通过比较返回码和内置的成功的返回码是一致的，来保证请求得到了正常响应，输出成功请求的消息，否则程序终止。

那么肯定不能只有`ok`这个条件码，还有没有其他的呢？答案是肯定的。

下面列出了返回码和相应的查询条件：

```python
    # Informational.
    100: ('continue',),
    101: ('switching_protocols',),
    102: ('processing',),
    103: ('checkpoint',),
    122: ('uri_too_long', 'request_uri_too_long'),
    200: ('ok', 'okay', 'all_ok', 'all_okay', 'all_good', '\\o/', '✓'),
    201: ('created',),
    202: ('accepted',),
    203: ('non_authoritative_info', 'non_authoritative_information'),
    204: ('no_content',),
    205: ('reset_content', 'reset'),
    206: ('partial_content', 'partial'),
    207: ('multi_status', 'multiple_status', 'multi_stati', 'multiple_stati'),
    208: ('already_reported',),
    226: ('im_used',),

    # Redirection.
    300: ('multiple_choices',),
    301: ('moved_permanently', 'moved', '\\o-'),
    302: ('found',),
    303: ('see_other', 'other'),
    304: ('not_modified',),
    305: ('use_proxy',),
    306: ('switch_proxy',),
    307: ('temporary_redirect', 'temporary_moved', 'temporary'),
    308: ('permanent_redirect',
          'resume_incomplete', 'resume',), # These 2 to be removed in 3.0

    # Client Error.
    400: ('bad_request', 'bad'),
    401: ('unauthorized',),
    402: ('payment_required', 'payment'),
    403: ('forbidden',),
    404: ('not_found', '-o-'),
    405: ('method_not_allowed', 'not_allowed'),
    406: ('not_acceptable',),
    407: ('proxy_authentication_required', 'proxy_auth', 'proxy_authentication'),
    408: ('request_timeout', 'timeout'),
    409: ('conflict',),
    410: ('gone',),
    411: ('length_required',),
    412: ('precondition_failed', 'precondition'),
    413: ('request_entity_too_large',),
    414: ('request_uri_too_large',),
    415: ('unsupported_media_type', 'unsupported_media', 'media_type'),
    416: ('requested_range_not_satisfiable', 'requested_range', 'range_not_satisfiable'),
    417: ('expectation_failed',),
    418: ('im_a_teapot', 'teapot', 'i_am_a_teapot'),
    421: ('misdirected_request',),
    422: ('unprocessable_entity', 'unprocessable'),
    423: ('locked',),
    424: ('failed_dependency', 'dependency'),
    425: ('unordered_collection', 'unordered'),
    426: ('upgrade_required', 'upgrade'),
    428: ('precondition_required', 'precondition'),
    429: ('too_many_requests', 'too_many'),
    431: ('header_fields_too_large', 'fields_too_large'),
    444: ('no_response', 'none'),
    449: ('retry_with', 'retry'),
    450: ('blocked_by_windows_parental_controls', 'parental_controls'),
    451: ('unavailable_for_legal_reasons', 'legal_reasons'),
    499: ('client_closed_request',),

    # Server Error.
    500: ('internal_server_error', 'server_error', '/o\\', '✗'),
    501: ('not_implemented',),
    502: ('bad_gateway',),
    503: ('service_unavailable', 'unavailable'),
    504: ('gateway_timeout',),
    505: ('http_version_not_supported', 'http_version'),
    506: ('variant_also_negotiates',),
    507: ('insufficient_storage',),
    509: ('bandwidth_limit_exceeded', 'bandwidth'),
    510: ('not_extended',),
    511: ('network_authentication_required', 'network_auth', 'network_authentication'),
```

比如如果你想判断结果是不是`404`状态，你可以用`requests.codes.not_found`来比对。

##### 响应头

如果想得到响应头信息，可以使用`headers`属性。它其实本质上也是一个字典形式。可以通过数组索引或者`get()`方法来获取某一条头信息内容。

比如获取`Content-Type`可以用`r.headers['Content-Type']`，也可以用`r.headers.get('content-type')`，是不是很方便呢？

好，至此我们介绍了利用`requests`模拟最基本的`GET`和`POST`请求的过程，关于更多高级的用法，会在下一节进行讲解。

