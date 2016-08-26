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

#### 基本GET请求

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




