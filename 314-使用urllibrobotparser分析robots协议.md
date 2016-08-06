### 使用urllib.robotparser分析robots协议

#### 什么是robots协议

要了解`urllib.robotparser`这个库，首先我们需要了解下什么是robots协议。

robots协议也被称作爬虫协议、机器人协议，它的全名叫做`网络爬虫排除标准（Robots Exclusion Protocol）`，同来告诉爬虫和搜索引擎哪些页面可以抓取，哪些不可以抓取。它通常是一个叫做`robots.txt`的文本文件，放在网站的根目录下。

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

| 蜘蛛名称 | 网站 |
| ---------| ------ |
| BaiduSpider | www.baidu.com |




















