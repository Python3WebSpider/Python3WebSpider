### 使用urllib.robotparser分析robots协议

#### 什么是robots协议

要了解`urllib.robotparser`这个库，首先我们需要了解下什么是`robots协议`。

`robots协议`也被称作爬虫协议、机器人协议，它的全名叫做`网络爬虫排除标准（Robots Exclusion Protocol）`，同来告诉爬虫和搜索引擎哪些页面可以抓取，哪些不可以抓取。它通常是一个叫做`robots.txt`的文本文件，放在网站的根目录下。

当一个搜索蜘蛛访问一个站点时，它首先会检查下这个站点根目录下是否存在`robots.txt`文件，如果存在，搜索蜘蛛会根据其中定义的爬取范围来爬取。如果没有找到这个文件，那么搜索蜘蛛便会访问所有可直接访问的页面。

下面我们用一个实例感受一下：

```
User-agent: *
Disallow: /
Allow: /public/
```

以上的两行实现了对所有搜索蜘蛛只允许爬取`public`目录的作用。

如上简单的两行，保存成`robots.txt`文件，放在网站的根目录下，和网站的入口文件放在一起。比如`index.php`、`index.html`、`index.jsp`等等。

那么上面的`User-agent`就描述了搜索蜘蛛的名称，在这里将值设置为`*`，则代表该协议对任何的爬取蜘蛛有效。比如你可以设置`User-agent: Baiduspider`，就代表我们设置的规则对百度搜索引擎是有效的。如果有多条`User-agent`记录，则就会有多个爬取蜘蛛会受到爬取限制，但你至少需要指定一条。

`Disallow`指定了不允许抓取的目录，比如上述例子中设置为`/`则代表不允许抓取所有页面。

`Allow`它一般和`Disallow`一起使用，一般不会单独使用，现在我们设置为`/public/`，起到的作用是所有页面不允许抓取，但是`public`目录是可以抓取的。

下面我们再来看几个例子感受一下：

##### 禁止所有搜索蜘蛛访问网站任何部分

```
User-agent: * 
Disallow: /
```

##### 允许所有搜索蜘蛛访问

```
User-agent: *
Disallow:
```

或者直接把`robots.txt`文件留空。

##### 禁止所有搜索蜘蛛访问网站某些目录

```
User-agent: *
Disallow: /private/
Disallow: /tmp/
```

##### 只允许某一个搜索蜘蛛访问

```
User-agent: WebCrawler
Disallow:
User-agent: *
Disallow: /
```

大家可能会疑惑，蜘蛛名是哪儿来的？为什么就叫这个名？其实它是有固定名字的了，比如百度的就叫做BaiduSpider，下面的表格列出了一些常见的搜索蜘蛛的名称及对应的网站。

| 蜘蛛名称 | 名称|网站 |
| ---------| -------- |
| BaiduSpider | 百度 | www.baidu.com |
| Googlebot| 谷歌 | www.google.com |
| 360Spider| 360搜索 | www.so.com |
| YodaoBot | 有道 | www.youdao.com |
| ia_archiver | Alexa | www.alexa.cn |
| Scooter | altavista | www.altavista.com |


#### 使用urllib.robotparser

了解了什么是`robots协议`之后，我们就可以使用`urllib.robotparser`来解析`robots.txt`了。

`urllib.parser`提供了一个类，叫做`RobotFileParser`。它可以根据某网站的`robots.txt`文件来判断一个爬取蜘蛛是否有权限来爬取这个网页。

使用非常简单，首先看一下它的声明

```python
urllib.robotparser.RobotFileParser(url='')
```

使用这个类的时候非常简单，只需要在构造方法里传入`robots.txt`的链接即可。当然也可以声明时不传入，默认为空，再使用`set_url(url)`方法设置一下也可以。

有常用的几个方法分别介绍一下：

`set_url(url)`用来设置`robots.txt`文件的链接。如果已经在创建`RobotFileParser`对象时传入了链接，那就不需要再使用这个方法设置了。

`read()`读取`robots.txt`文件并进行分析，注意这个函数是执行一个读取和分析操作，如果不调用这个方法，接下来的判断都会为`False`，所以一定记得调用这个方法，这个方法不会返回任何内容，但是执行了读取操作。

`parse(lines)`方法是用来解析`robots.txt`文件的，传入的参数是`robots.txt`某些行的内容，它会按照`robots.txt`的语法规则来分析这些内容。

`can_fetch(useragent, url)`方法传入两个参数，第一个是`User-agent`，第二个是要抓取的`URL`，返回的内容是该搜索引擎是否可以抓取这个`URL`，返回结果是`True`或`False`。

`mtime()`返回的是上次抓取和分析`robots.txt`的时间，这个对于长时间分析和抓取的搜索蜘蛛是很有必要的，你可能需要定期检查来抓取最新的`robots.txt`。

`modified()`同样的对于长时间分析和抓取的搜索蜘蛛很有帮助，将当前时间设置为上次抓取和分析`robots.txt`的时间。

以上是这个类提供的所有方法，下面我们用实例来感受一下：

```python
from urllib.robotparser import RobotFileParser

rp = RobotFileParser()
rp.set_url('http://www.jianshu.com/robots.txt')
rp.read()
print(rp.can_fetch('*', 'http://www.jianshu.com/p/b67554025d7d'))
print(rp.can_fetch('*', "http://www.jianshu.com/search?q=python&page=1&type=collections"))

```

以简书为例，我们首先创建`RobotFileParser`对象，然后通过`set_url()`方法来设置了`robots.txt`的链接。当然不用这个方法的话，可以在声明时直接用`rp = RobotFileParser('http://www.jianshu.com/robots.txt')`声明对象，下一步关键的，执行读取和分析。然后后面利用了`can_fetch()`方法来判断了网页是否可以被抓取。

运行结果：

```
True
False
```

同样也可以使用`parser()`方法执行读取和分析。

用一个实例感受一下：

```python
from urllib.robotparser import RobotFileParser
from urllib.request import urlopen

rp = RobotFileParser()
rp.parse(urlopen('http://www.jianshu.com/robots.txt').read().decode('utf-8').split('\n'))
print(rp.can_fetch('*', 'http://www.jianshu.com/p/b67554025d7d'))
print(rp.can_fetch('*', "http://www.jianshu.com/search?q=python&page=1&type=collections"))
```

运行结果一样：

```
True
False
```

以上介绍了`urllib.robotparser`的基本用法和实例讲解，利用它我们就可以方便地判断哪些页面可以抓取哪些不可以了。