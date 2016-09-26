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


#### 设置超时

```python
r.headers.get('content-type')
```


