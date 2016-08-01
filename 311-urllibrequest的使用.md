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

输出结果

```
<class 'http.client.HTTPResponse'>

```

通过输出结果可以发现它是一个HTTPResposne类型的对象，它主要包含的方法有`read()`,`readinto()`,`getheader()`,`getheaders()`,`fileno()`等函数和`msg`,`version`,`status`,`reason`,`debuglevel`,`closed`等属性。
得到这个对象之后，赋值为response，然后就可以用response调用这些方法和属性，得到返回结果的一系列信息。

例如`response.read()`就可以得到返回的网页内容，`response.status`就可以得到返回结果的状态码，如200代表请求成功，404代表网页未找到等。














