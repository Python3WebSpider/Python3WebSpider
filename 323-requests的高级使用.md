### requests的高级使用

#### 文件上传

```python
import requests

files = {'file': open('favicon.ico', 'rb')}
r = requests.post("http://httpbin.org/post", files=files)
print(r.text)
```

#### 设置超时

```python
r.headers.get('content-type')
```


