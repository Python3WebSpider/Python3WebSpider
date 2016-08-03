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

#### urllib.error.HTTPError

它是刚才介绍的`URLError`的子类，专门用来处理`HTTP`请求错误，比如认证请求失败等等。

它有三个属性。

`code`，返回`HTTP`状态码，比如404网页不存在，500服务器内部错误等等。

`reason`，同父类一样，返回错误的原因。

`headers`，返回`HTTP`响应头。

下面用表格列出了常见的错误代码及错误原因。

| 状态码  | 说明        | 详情                                 |
| ---- | :-------- | :--------------------------------- |
| 100  | 继续        | 请求者应当继续提出请求。服务器已收到请求的一部分，正在等待其余部分。 |
| 101  | 切换协议      | 请求者已要求服务器切换协议，服务器已确认并准备切换。         |
| 200  | 成功        | 服务器已成功处理了请求。                       |
| 201  | 已创建       | 请求成功并且服务器创建了新的资源。                  |
| 202  | 已接受       | 服务器已接受请求，但尚未处理。                    |
| 203  | 非授权信息     | 服务器已成功处理了请求，但返回的信息可能来自另一来源。        |
| 204  | 无内容       | 服务器成功处理了请求，但没有返回任何内容。              |
| 205  | 重置内容      | 服务器成功处理了请求，内容被重置。                  |
| 206  | 部分内容      | 服务器成功处理了部分请求。                      |
| 300  | 多种选择      | 针对请求，服务器可执行多种操作。                   |
| 301  | 永久移动      | 请求的网页已永久移动到新位置，即永久重定向。             |
| 302  | 临时移动      | 请求的网页暂时跳转到其他页面，即暂时重定向。             |
| 303  | 查看其他位置    | 如果原来的请求是POST，重定向目标文档应该通过GET提取。     |
| 304  | 未修改       | 此次请求返回的网页未修改，继续使用上次的资源。            |
| 305  | 使用代理      | 请求者应该使用代理访问该网页。                    |
| 307  | 临时重定向     | 请求的资源临时从其他位置响应。                    |
| 400  | 错误请求      | 服务器无法解析该请求。                        |
| 401  | 未授权       | 请求没有进行身份验证或验证未通过。                  |
| 403  | 禁止访问      | 服务器拒绝此请求。                          |
| 404  | 未找到       | 服务器找不到请求的网页。                       |
| 405  | 方法禁用      | 服务器禁用了请求中指定的方法。                    |
| 406  | 不接受       | 无法使用请求的内容响应请求的网页。                  |
| 407  | 需要代理授权    | 请求者需要使用代理授权。                       |
| 408  | 请求超时      | 服务器请求超时。                           |
| 409  | 冲突        | 服务器在完成请求时发生冲突。                     |
| 410  | 已删除       | 请求的资源已永久删除。                        |
| 411  | 需要有效长度    | 服务器不接受不含有效内容长度标头字段的请求。             |
| 412  | 未满足前提条件   | 服务器未满足请求者在请求中设置的其中一个前提条件。          |
| 413  | 请求实体过大    | 请求实体过大，超出服务器的处理能力。                 |
| 414  | 请求URI过长   | 请求网址过长，服务器无法处理。                    |
| 415  | 不支持类型     | 请求的格式不受请求页面的支持。                    |
| 416  | 请求范围不符    | 页面无法提供请求的范围。                       |
| 417  | 未满足期望值    | 服务器未满足期望请求标头字段的要求。                 |
| 500  | 服务器内部错误   | 服务器遇到错误，无法完成请求。                    |
| 501  | 未实现       | 服务器不具备完成请求的功能。                     |
| 502  | 错误网关      | 服务器作为网关或代理，从上游服务器收到无效响应。           |
| 503  | 服务不可用     | 服务器目前无法使用。                         |
| 504  | 网关超时      | 服务器作为网关或代理，但是没有及时从上游服务器收到请求。       |
| 505  | HTTP版本不支持 | 服务器不支持请求中所用的 HTTP 协议版本。            |

下面我们来用几个实例感受一下：

```python
# coding=utf-8
from urllib import request,error
try:
    response = request.urlopen('http://cuiqingcai.com/index.htm')
except error.HTTPError as e:
    print(e.reason, e.code, e.headers, seq='\n')
```
运行结果：

```
Not Found
404
Server: nginx/1.4.6 (Ubuntu)
Date: Wed, 03 Aug 2016 08:54:22 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: close
X-Powered-By: PHP/5.5.9-1ubuntu4.14
Vary: Cookie
Expires: Wed, 11 Jan 1984 05:00:00 GMT
Cache-Control: no-cache, must-revalidate, max-age=0
Pragma: no-cache
Link: <http://cuiqingcai.com/wp-json/>; rel="https://api.w.org/"
```

依然是同样的网址，在这里我们捕获了`HTTPError`，输出了错误原因、错误代号、服务器响应头。

因为`URLError`是`HTTPError`的父类，所以我们可以先选择捕获子类的错误，再去捕获父类的错误，更好的写法如下：

```python
# coding=utf-8
from urllib import request, error

try:
    response = request.urlopen('http://cuiqingcai.com/index.htm')
except error.HTTPError as e:
    print(e.reason, e.code, e.headers, sep='\n')
except error.URLError as e:
    print(e.reason)
else:
    print('Request Successfully')

```









