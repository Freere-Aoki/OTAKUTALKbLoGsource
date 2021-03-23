---
title: python爬虫-爬取steam统计数据（steam游戏在线人数）
date: 2021-03-22 16:10:24
tags:
  - study
  - python
---

[转自[阿里波特]-[Python爬虫小白入门]](https://www.cnblogs.com/Albert-Lee/p/6226699.html)

# 前言

------

你是不是在为想收集数据而不知道如何收集而着急？

你是不是在为想学习爬虫而找不到一个专门为小白写的教程而烦恼？

Bingo! 你没有看错，这就是专门面向小白学习爬虫而写的！我会采用实例的方式，把每个部分都跟实际的例子结合起来帮助小伙伴儿们理解。最后再写几个实战的例子。

我们使用Python来写爬虫，一方面因为Python是一个特别适合变成入门的语言，另一方面，Python也有很多爬虫相关的工具包，能够简单快速的开发出我们的小爬虫。
本系列采用Python3.5版本，毕竟2.7会慢慢退出历史舞台~

那么，接下来，你得知道什么是爬虫、爬虫从哪里爬取数据的，以及，学习爬虫都要学习哪些东西。

# 什么是爬虫

------

来看看百度百科是如何定义的

> [网络爬虫](http://baike.baidu.com/view/284853.htm)（又被称为网页[蜘蛛](http://baike.baidu.com/subview/8483/5395928.htm)，网络机器人，在[FOAF](http://baike.baidu.com/view/271451.htm)社区中间，更经常的称为网页追逐者），是一种按照一定的规则，自动地抓取[万维网](http://baike.baidu.com/view/7833.htm)信息的程序或者脚本。另外一些不常使用的名字还有[蚂蚁](http://baike.baidu.com/subview/3312/6169348.htm)、自动索引、模拟程序或者[蠕虫](http://baike.baidu.com/view/2596.htm)。

什么？没看懂？没关系，我来给你解释一下

打开一个网页，里面有网页内容吧，想象一下，有个工具，可以把网页上的内容获取下来，存到你想要的地方，这个工具就是我们今天的主角：爬虫。

这样是不是更清晰了呢？

既然了解了爬虫是什么，那么爬虫是如何爬取数据的呢？

# 爬虫是哪里爬取数据的

------

打开浏览器（强烈建议谷歌浏览器），找到浏览器地址栏，然后在里输入store.steampowered.com/stats/并回车，你会看到网页内容。

<img class="litimg" src="\img\QQ截图20210322162126.png" alt="QQ截图20210322162126" style="zoom:100%;" />

摁下键盘上的F12打开开发调试工具,，然后点击元素。看到这些文字了吗？这才是网页最赤果果的样子。

<img class="litimg" src="\img\QQ截图20210322162407.png" alt="QQ截图20210322162407" style="zoom:100%;" />

其实所有的网页都是HTML代码，只不过浏览器将这些代码解析成了上面的网页，我们的爬虫抓取的其实就是HTML代码中的文本。

随后点击开发调试工具左上角的小鼠标图标，点击你想要爬取的数据，你就会在元素这一栏看到下面红框的地方，是你要爬取数据的本来模样。

<img class="litimg" src="\img\QQ截图20210322162801.png" alt="QQ截图20210322162801" style="zoom:100%;" />

没错，我们的爬虫抓取的正是网页中的数据，你要知道你想要抓取什么数据，你的目标网站是什么，才可以把想法变成现实的。

# 学习爬虫的必备知识

------

大家要先对以下内容有一定的了解再来学习爬虫哦，磨刀不误砍柴工

- HTML
  这个能够帮助你了解网页的结构，内容等。可以参考[W3School的教程](http://www.w3school.com.cn/html/index.asp)。
- Python
  如果有编程基础的小伙伴儿，推荐看一个[廖雪峰的Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)就够了
  没有编程基础的小伙伴，推荐看看视频教程（网易云课堂搜Python），然后再结合廖雪峰的教程，双管齐下。
  其实知乎上总结的已经非常好了，我就不多唠叨了。[知乎-如何系统的自学Python](https://www.zhihu.com/question/29138020)
- TCP/IP协议，HTTP协议
  这些知识能够让你了解在网络请求和网络传输上的基本原理，了解就行，能够帮助今后写爬虫的时候理解爬虫的逻辑。
  廖雪峰Python教程里也有简单介绍，可以参考：[TCP/IP简介](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014320037768360d53e4e935ca4a1f96eed1c896ad1217000)，[HTTP协议](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432011939547478fd5482deb47b08716557cc99764e0000)
  想更深入学习的小伙伴儿可以去网上多搜搜相关的书籍哦

OK, 下一步就开始我们的实战啦

# requests 库的安装

为什么要先说Requests库呢，因为这是个功能很强大的网络请求库，可以实现跟浏览器一样发送各种HTTP请求来获取网站的数据。网络上的模块、库、包指的都是同一种东西，所以后文中可能会在不同地方使用不同称谓，不要迷惑。

直接使用Python3.5的小伙伴儿输入这个命令：
`pip install requests`

如果你机器上存在多个Python版本，要给Python3.5的版本安装requests库，需要输入以下命令：
`py -3 -m pip install requests`

好啦，requests库安装完毕，接下来我们会在实际例子中演示它的使用。想要深入了解requests模块的小伙伴也可以仔细阅读[英文官方文档](http://docs.python-requests.org/en/master/api/)，和[中文官方文档](http://docs.python-requests.org/zh_CN/latest/user/quickstart.html)，如果用到该文没有提到的功能，则查看文档即可。

# 开工

------

新建一个文件，只要他的后缀为.py即可，名字选择你喜欢的就好。

输入第一行代码来导入requests库：
`import requests #导入requests库`

然后用它来获取咱们的目标网页：

```python
r = requests.get('https://store.steampowered.com/stats/') #像目标url地址发送get请求，返回一个response对象
print(r.text) #r.text是http response的网页HTML
```

之后再文件目录打开cmd页面，指令运行

`python xxxxx.py`

执行完之后，底部会出现输出结果：

<img class="litimg" src="\img\QQ截图20210322163703.png" alt="QQ截图20210322163703" style="zoom:70%;" />

可以看到底部是获取到的网页内容。这就完成了爬虫的第一步，获取到了网页的HTML内容。

这用到了requests库的get请求。其他请求使用也与之类似，但本文不进行阐述。

我们刚才用requests库发送http请求获得了网页的HTML内容，那么应该如何从HTML中获得图片？

BeautifulSoup库就此登场啦，赶快去来了解它的用法。

刚才演示了如何使用requests模块向网站发送http请求，获取到网页的HTML数据。这篇来演示如何使用BeautifulSoup模块来从HTML文本中提取我们想要的数据。

# 模块安装

------

BeautifulSoup 有多个版本，我们使用BeautifulSoup4。详细使用看[BeautifuSoup4官方文档](http://beautifulsoup.readthedocs.io/zh_CN/latest/)。
使用管理员权限打开cmd命令窗口，在窗口中输入下面的命令即可安装：
`pip install beautifulsoup4`

然后我们安装lxml，这是一个解析器，BeautifulSoup可以使用它来解析HTML，然后提取内容。

`pip install lxml`

如果不安装lxml，则BeautifulSoup会使用Python内置的解析器对文档进行解析。之所以使用lxml，是因为它速度快。

# BeautifulSoup 库的使用

------

网上找到的几个官方文档：[BeautifulSoup4.4.0中文官方文档](http://beautifulsoup.readthedocs.io/zh_CN/latest/)，[BeautifulSoup4.2.0中文官方文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)。不同版本的用法差不多，几个常用的语法都一样。

首先来看BeautifulSoup的对象种类，在使用的过程中就会了解你获取到的东西接下来应该如何操作。

## BeautifulSoup对象的类型

Beautiful Soup将复杂HTML文档转换成一个复杂的树形结构，每个节点都是Python对象。所有对象可以归纳为4种类型: Tag , NavigableString , BeautifulSoup , Comment 。下面我们分别看看这四种类型都是什么东西。

### Tag

这个就跟HTML或者XML（还能解析XML？是的，能！）中的标签是一样一样的。我们使用find()方法返回的类型就是这个（插一句：使用find-all()返回的是多个该对象的集合，是可以用for循环遍历的。）。返回标签之后，还可以对提取标签中的信息。

###### 提取标签的名字：

```javascript
tag.name
```

###### 提取标签的属性：

```javascript
tag['attribute']
```

### NavigableString

NavigableString就是标签中的文本内容（不包含标签）。获取方式如下：
`tag.string`
还是以上面那个例子，加上下面这行，然后执行：
`print('NavigableString is：', find.string)`

### BeautifulSoup

BeautifulSoup对象表示一个文档的全部内容。支持遍历文档树和搜索文档树。

### Comment

这个对象其实就是HTML和XML中的注释。

### 搜索文档树

最常用的当然是find()和find_all()啦，当然还有其他的。比如find_parent() 和 find_parents()、 find_next_sibling() 和 find_next_siblings() 、find_all_next() 和 find_next()、find_all_previous() 和 find_previous() 等等。
我们就看几个常用的，其余的如果用到就去看官方文档哦。

- find_all()
  搜索当前tag的所有tag子节点，并判断是否符合过滤器的条件。返回值类型是bs4.element.ResultSet。
  完整的语法：
  `find_all( name , attrs , recursive , string , **kwargs )`

# 继续之前的爬取

------

我们选中想要爬取的地方，查看html代码。发现游戏的在线人数都在span标签里，并且class都是currentServers，如下图。

<img class="litimg" src="\img\QQ截图20210322165150.png" alt="QQ截图20210322165150" style="zoom:70%;" />

通过观察，发现在线人数都在span元素中的文本内容。下面，我们先获取到所有的含有数据的span标签，然后在循环获取span标签中的文本内容。

```python
import requests #导入requests 模块
from bs4 import BeautifulSoup  #导入BeautifulSoup 模块

headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36'}  #给请求指定一个请求头来模拟chrome浏览器
web_url = 'https://store.steampowered.com/stats/'
r = requests.get(web_url, headers=headers) #像目标url地址发送get请求，返回一个response对象
all_a = BeautifulSoup(r.text, 'lxml').find_all('span', class_='currentServers')  #获取网页中的class为currentServers的所有span标签
for a in all_a:
  print(a.string) #循环获取a标签中的style
```

这里的find_all('span', class_='currentServers')是找到所有class为currentServers的span标签，返回的是一个list，所以可以用for循环获取每个span标签。

力荐[转自[阿里波特]-[Python爬虫小白入门]](https://www.cnblogs.com/Albert-Lee/p/6226699.html)OvO

<img class="litimg" src="\img\bbe600fa828ba61e7b3276565634970a314e593a.gif" alt="bbe600fa828ba61e7b3276565634970a314e593a" style="zoom:100%;" />

***`Excalibur！！！`***