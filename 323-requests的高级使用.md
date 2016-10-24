### requests的高级使用

#### 文件上传

我们知道`reqeuests`可以模拟提交一些数据，假如有的网站需要我们上传文件，我们同样可以利用它来上传，实现非常简单。

用一个实例来感受一下：

```python
# coding=utf-8
import requests

files = {'file': open('favicon.ico', 'rb')}
r = requests.post("http://httpbin.org/post", files=files)
print(r.text)
```
在上面一节中我们下载保存了一个文件叫做`favicon.ico`，这次我们用它为例来模拟文件上传的过程。需要注意的是，`favicon.ico`这个文件需要和当前脚本在同一目录下。如果有其它文件，当然也可以使用其它文件来上传，更改下名称即可。

运行结果如下：

```
{
  "args": {}, 
  "data": "", 
  "files": {
    "file": "data:application/octet-stream;base64,AAAAAA...="
  }, 
  "form": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "6665", 
    "Content-Type": "multipart/form-data; boundary=809f80b1a2974132b133ade1a8e8e058", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.10.0"
  }, 
  "json": null, 
  "origin": "60.207.237.16", 
  "url": "http://httpbin.org/post"
}

```

以上部分内容省略，这个网站会返回一个响应，里面包含`files`这个字段，而`form`是空的，这证明文件上传部分，会单独有一个`files`来标识。

#### Cookies处理

在前面我们使用了`urllib`，让它处理`cookies`真的是挺麻烦的，而有了`requests`，获得和提交`cookies`只需要一步。

我们先用一个实例感受一下获取`Cookies`的过程：

```python
# coding=utf-8
import requests

r = requests.get("https://www.baidu.com")
print(r.cookies)
for key, value in r.cookies.items():
    print(key + '=' + value)
```

运行结果如下：

```
<RequestsCookieJar[<Cookie BDORZ=27315 for .baidu.com/>, <Cookie __bsi=13533594356813414194_00_14_N_N_2_0303_C02F_N_N_N_0 for .www.baidu.com/>]>
BDORZ=27315
__bsi=13533594356813414194_00_14_N_N_2_0303_C02F_N_N_N_0
```

首先打印输出了`cookie`，可以发现它是一个`RequestCookieJar`类型。

然后用`items()`方法将其转化为元组组成的列表，遍历输出每一个`cookie`的名和值。

当然，你也可以直接用`Cookie`来维持登录状态。

比如我们以知乎为例，直接利用`Cookie`来维持登录状态。

首先登录知乎，将请求头中的`Cookie`复制下来。

![](/assets/3-2-4.png)

将其设置到`headers`里面，发送请求：

```python
# coding=utf-8
import requests

headers = {
    'Cookie': 'q_c1=31653b264a074fc9a57816d1ea93ed8b|1474273938000|1474273938000; d_c0="AGDAs254kAqPTr6NW1U3XTLFzKhMPQ6H_nc=|1474273938"; __utmv=51854390.100-1|2=registration_date=20130902=1^3=entry_date=20130902=1;a_t="2.0AACAfbwdAAAXAAAAso0QWAAAgH28HQAAAGDAs254kAoXAAAAYQJVTQ4FCVgA360us8BAklzLYNEHUd6kmHtRQX5a6hiZxKCynnycerLQ3gIkoJLOCQ==";z_c0=Mi4wQUFDQWZid2RBQUFBWU1DemJuaVFDaGNBQUFCaEFsVk5EZ1VKV0FEZnJTNnp3RUNTWE10ZzBRZFIzcVNZZTFGQmZn|1474887858|64b4d4234a21de774c42c837fe0b672fdb5763b0',
    'Host': 'www.zhihu.com',
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36',
}
r = requests.get("http://www.zhihu.com", headers=headers)
print(r.text)
```
发现结果中包含了正常的登录信息。

![](/assets/3-2-5.png)

登录成功！

当然也可以通过cookies参数来设置，不过这样就需要构造`RequestsCookieJar`对象，而且需要分割一下`Cookie`变量，相对繁琐，不过效果是相同的。

```python
# coding=utf-8
import requests

cookies = 'q_c1=31653b264a074fc9a57816d1ea93ed8b|1474273938000|1474273938000; d_c0="AGDAs254kAqPTr6NW1U3XTLFzKhMPQ6H_nc=|1474273938"; __utmv=51854390.100-1|2=registration_date=20130902=1^3=entry_date=20130902=1;a_t="2.0AACAfbwdAAAXAAAAso0QWAAAgH28HQAAAGDAs254kAoXAAAAYQJVTQ4FCVgA360us8BAklzLYNEHUd6kmHtRQX5a6hiZxKCynnycerLQ3gIkoJLOCQ==";z_c0=Mi4wQUFDQWZid2RBQUFBWU1DemJuaVFDaGNBQUFCaEFsVk5EZ1VKV0FEZnJTNnp3RUNTWE10ZzBRZFIzcVNZZTFGQmZn|1474887858|64b4d4234a21de774c42c837fe0b672fdb5763b0'
jar = requests.cookies.RequestsCookieJar()
headers = {
    'Host': 'www.zhihu.com',
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36'
}
for cookie in cookies.split(';'):
    key, value = cookie.split('=', 1)
    jar.set(key, value)
r = requests.get("http://www.zhihu.com", cookies=jar, headers=headers)
print(r.text)
```
上面我们首先新建了一个`RequestCookieJar`对象，然后将复制下来的`Cookie`利用`split()`方法分割，利用`set()`方法设置好每一个`Cookie`的`key`和`value`，然后通过`requests.get()`方法的`cookies`参数设置即可，当然由于知乎本身的限制，`headers`变量不能少，只不过不需要在原来的`headers`里面设置`Cookie`字段了。

测试后，发现同样可以正常登录知乎。

#### 会话维持

在`requests`中，我们如果直接利用`requests.get()`或`requests.post()`等方法的确可以做到模拟网页的请求。但是这实际上是相当于不同的会话，即不同的`session`，也就是说相当于你用了两个浏览器打开了不同的页面。

设想这样一个场景，你第一个请求利用了`requests.post()`方法登录了某个网站，第二次想获取成功登录后的自己的个人信息，你又用了一次`requests.get()`方法。实际上，这相当于打开了两个浏览器，是两个完全不相关的会话，你说你能成功获取个人信息吗？那当然不能。

有小伙伴就说了，我在两次请求的时候都设置好一样的`Cookie`不就行了？行是行，但是不觉得麻烦吗？每次都要这样。是我我忍不了。

其实解决这个问题的主要方法就是维持同一个会话，也就是相当于打开一个新的浏览器选项卡而不是新开一个浏览器。但是我又不想每次设置`Cookie`，那该咋办？这时候就有了新的利器`Session`。

利用它，我们可以方便地维护一个会话，而且不用担心`Cookie`的问题，它会帮我们自动处理好。

下面用一个实例来感受一下：

```python
# coding=utf-8
import requests

requests.get('http://httpbin.org/cookies/set/number/123456789')
r = requests.get('http://httpbin.org/cookies')
print(r.text)
```

在实例中我们请求了一个测试网址，`http://httpbin.org/cookies/set/number/123456789`请求这个网址我们可以设置一`Cookie`，名称叫做`number`，内容是`123456789`，后面的网址`http://httpbin.org/cookies`可以获取当前的`Cookie`。

你觉得这样能成功获取到设置的`Cookie`吗？试试看。

运行结果如下：

```python
{
  "cookies": {}
}
```

喔并不行。那这时候我们想起刚才说的`Session`了，改成这个试试看：

```python
# coding=utf-8
import requests

s = requests.Session()
s.get('http://httpbin.org/cookies/set/number/123456789')
r = s.get('http://httpbin.org/cookies')
print(r.text)
```

这下呢？看下运行结果：

```python
{
  "cookies": {
    "number": "123456789"
  }
}
```

嗯，成功获取！这下能体会到同一个会话和不同会话的区别了吧？

所以，利用`Session`我们可以做到模拟同一个会话，而且不用担心`Cookie`的问题，通常用于模拟登录成功之后再进行下一步的操作。

`Session`在平常用到的非常广泛，可以用于模拟在一个浏览器中打开同一站点的不同页面。






