### requests的基本使用

#### 实例引入

在`urllib`库中，有`urllib.request.urlopen(url)`的方法，实际上它是以`GET`方式请求了一个网页。

那么在`requests`中，相应的方法就是`requests.get(url)`，是不是感觉表达更明确一些？

下面我们用一个实例来感受一下：

```python
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

怎么样？是不是比`urllib`太多了？

其实这只是冰山一角，更多的还在后面呢。

#### GET请求

HTTP中最常见的请求之一就是`GET`请求，我们首先来详细了解下利用`requests`来构建`GET`请求的方法以及相关属性方法操作。

首先让我们来构建一个最简单的`GET`请求，请求`httpbin.org/get`，它会判断如果你是`GET`请求的话，会返回响应的请求信息。

```python
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

#### 抓取网页

如上的请求链接返回的是`Json`形式的字符串，那么如果我们请求普通的网页，那么肯定就能获得相应的内容了。

下面我们以知乎－发现页面为例来体验一下：

```python
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

```
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
import requests

r = requests.get("https://github.com/favicon.ico")
with open('favicon.ico', 'wb') as f:
    f.write(r.content)
    f.close()
```

运行结束之后，可以发现在文件夹中出现了名为`favicon.ico`的图标。














