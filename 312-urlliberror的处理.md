### urllib.error的处理

`urllib.error`模块定义了由`urllib.request`产生的异常。如果出现了问题，`urllib.request`便会抛出`urllib.error`，下面会对其进行详细的介绍。

#### urllib.error.URLError

它继承自`OSError`，是`urllib.error`异常类的基类，由`urllib.request`产生的异常都可以通过捕获这个类来处理。

它具有一个属性`reason`，即返回错误的原因。

下面用一个例子来感受一下。

```python
# coding=utf-8
from urllib import request,error
try:
    response = request.urlopen('http://cuiqingcai.com/index.htm')
except error.URLError as e:
    print(e.reason)
```

我们打开一个不存在的页面，然后捕获了`URLError`这个异常。

运行结果：

```
Not Found
```

这样通过如上操作，避免了程序异常终止，同时异常得到了有效处理。





































