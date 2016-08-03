### 使用urllib.parse解析链接

这个模块定义了处理`URL`的标准接口，例如实现`URL`各部分的抽取，合并以及链接转换。它支持如下类型的链接处理：`file`、`ftp`、`gopher`、`hdl`、`http`、`https`、`imap`、`mailto`、 `mms`、`news`、`nntp`、`prospero`、`rsync`、`rtsp`、`rtspu`、`sftp`、`shttp`、 `sip`、`sips`、`snews`、`svn`、`svn+ssh`、`telnet`、`wais`。

常用的函数有`urllib.parse.urlparse()`，首先看一下它的`API`。

```python
urllib.parse.urlparse(urlstring, scheme='', allow_fragments=True)
```

它有三个参数，第一个参数是必填项，即待解析的`URL`。

返回结果是一个



































