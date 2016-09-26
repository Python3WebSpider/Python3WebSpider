### requests的高级使用

#### 文件上传

我们知道`reqeuests`可以模拟提交一些数据，假如有的网站需要我们上传文件，我们同样可以利用它来上传，实现非常简单。

用一个实例来感受一下：

```python
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




#### 设置超时

```python
r.headers.get('content-type')
```


