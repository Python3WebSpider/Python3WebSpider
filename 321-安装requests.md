### 安装requests

由于`requests`属于第三方库，也就是`python`默认不会自带这个库，需要我们手动去安装，下面我们首先看一下它的安装过程。

#### pip安装

无论是Windows、Linux还是Mac，你都可以通过pip这个包管理工具来安装。

在命令行下运行如下命令：

```
pip3 install requests
```

这是最简单的安装方式，推荐此种方法。

### 源码安装

那么如果你没有pip或者不想用pip来安装，或者想获取某一特定版本，可以选择下载源码安装。

项目的地址是：

[https://github.com/kennethreitz/requests](https://github.com/kennethreitz/requests)

你可以通过下面的方式来下载源代码：


```
git clone git://github.com/kennethreitz/requests.git

```

或

```
curl -OL https://github.com/kennethreitz/requests/tarball/master

```
或者直接点击下载压缩包。

下载下来之后，进入目录，执行安装即可。

```
cd requests
python3 setup.py install
```

即可完成`requests`的安装。

为了证明你已经安装成功，可以在命令行下测试一下。

```bash
➜  / python3
Python 3.5.1 (v3.5.1:37a07cee5969, Dec  5 2015, 21:12:44) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import requests
>>> 

```

在命令行首先输入`python3`，进入命令行模式，然后输入`import requests`，如果什么错误提示也没有，那么就证明你已经成功安装了`requests`。







































