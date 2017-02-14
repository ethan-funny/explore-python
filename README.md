![cover](https://ofaatpail.qnssl.com/2017-01-03-explore-python-cover.png)

Python 之旅
===

![Version](https://img.shields.io/badge/version-1.0-brightgreen.svg)
[![License: CC BY-NC-ND 4.0](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-brightgreen.svg)](https://raw.githubusercontent.com/ethan-funny/explore-python/master/LICENSE)
[![Analytics](https://ga-beacon.appspot.com/UA-62960961-2/explore-python/readme)](https://github.com/igrigorik/ga-beacon)

## Python 简介

Python 诞生于 1989 年的圣诞期间，由 [Guido van Rossum](https://gvanrossum.github.io/) 开发而成，目前 Guido 仍然是 Python 的主要开发者，主导着 Python 的发展方向，Python 社区经常称呼他为『仁慈的独裁者』。

Python 是一门面向对象，解释型的高级程序设计语言，它的语法非常简洁、优雅，而这也是 Python 的一些设计哲学。Python 自带了很完善的库，涵盖了数据库，网络，文件处理，GUI 等方方面面，通过这些库，我们可以比较快速地解决一些棘手问题，也可以将其作为基础库，开发出一些高级库。

目前 Python 在大部分领域都占有一席之地，比如 Web 开发，机器学习，科学计算等。不少大型网站都是使用 Python 作为后台开发语言的，比如 [YouTube](https://www.youtube.com/)、[Pinterest](https://www.pinterest.com/)、国内的[豆瓣](https://www.douban.com/)和[知乎](http://www.zhihu.com/)等。另外，有不少知名的机器学习库也是使用 Python 开发的，比如，[scikit-learn](http://scikit-learn.org/stable/) 是一个强大的机器学习库，[Theano](https://github.com/Theano/Theano) 是一个成熟的深度学习库。

当然了，Python 也有一些缺点。Python 经常被人们吐槽的一点就是：运行速度慢，和 C/C++ 相比非常慢。但是，除了像视频高清解码等**计算密集型任务**对运行速度有较高的要求外，在大部分时候，我们可能并不需要非常快的运行速度。比如，一个程序使用 C 来实现，运行时间只需 0.01 秒，而使用 Python 来实现，需要 0.1 秒，虽然 Python 的运行时间是 C 的 10 倍，显然很慢，但对我们而言，这压根不是问题。

## 关于本书

本书是我学习和使用 Python 的总结。在学习和使用 Python 的过程中，我作了不少笔记，并对一些笔记进行了加工和完善，发表在博客上。随着笔记的增加，我就萌生了写一本书的想法，希望能比较系统地总结相关知识，巩固自己的知识体系，而不是停留在『感觉好像懂了』的状态中。

有了想法之后，接下来就要开始写了。当然，从产生想法到付诸实践还是纠结了一段时间，毕竟，作笔记和写书很不一样啊。思想斗争过后，我下定决心要把它写出来。

首先，我参考一些相关的书籍，作了一个基础的思维导图，如下：

![思维导图](https://ofaatpail.qnssl.com/2017-01-03-explore-python2.png)

接下来，就要开始写作了，这也是最艰难的一关。

我没有按照从头到尾的顺序写，而是从最感兴趣的知识点入手，比如函数式编程、类的使用等等。就这样，一点一点地写，实在不想写了，就先搁置一下，过两天继续写。

我在写作的过程中，给自己提了一个要求：**尽量深入浅出，条理清晰**。至于是否达到了，希望读者们多多批评指正，并给我提意见和建议。

本书的每章基本上都是独立的，读者可以挑选感兴趣的章节进行阅读。目前本书有 15 个章节：

- 第 1 章：介绍一些基础知识，包括 Python 中的输入和输出，字符编码。
- 第 2 章：介绍常用数据类型，比如字符串、列表和字典等。
- 第 3 章：介绍函数的定义和函数参数魔法。
- 第 4 章：介绍 Python 中的函数式编程，包括匿名函数、闭包和装饰器等。
- 第 5 章：介绍 Python 中类的使用，包括类方法、静态方法、super 和元类的使用等。
- 第 6 章：介绍 Python 中的高级特性，比如生成器，上下文管理器。
- 第 7 章：介绍文件和目录操作，os 的使用。
- 第 8 章：介绍使用 Python 处理进程、线程和协程。
- 第 9 章：异常处理。
- 第 10 章：单元测试。
- 第 11 章：正则表达式，re 模块的使用。
- 第 12 章：HTTP 服务，requests 模块的使用。
- 第 13 章：一些标准模块的使用，比如 argparse、collections 和 datetime 等。
- 第 14 章：一些第三方模块的使用。
- 第 15 章：结束语。

本书的编码环境：

- Python 版本以 2.7 为主，同时也会指出在 Python3 中的相应变化
- 操作系统使用 macOS，代码结果，尤其是内存地址等由于运行环境的不同会存在差异

本书将会持续进行修订和更新，读者如果遇到问题，请及时向我反馈，我会在第一时间加以解决。

## 下载电子版

目前本书暂时提供 epub 格式的电子版，因为生成的 pdf 和 mobi 版本并不是很美观，不利于阅读，故暂不提供。

[点击下载 epub 版本](https://github.com/ethan-funny/explore-python/files/691859/explore-python.epub.zip)

## 声明

<a rel="license" href="https://creativecommons.org/licenses/by-nc-nd/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png" /></a>

本书由 [Ethan](https://github.com/ethan-funny) 编写，采用 [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.zh) 协议发布。

这意味着你可以在非商业性使用的前提下自由转载，但必须：

1. 保持署名
2. 不对本书进行修改

## 更新记录

| 时间 | 说明 |
| :---: | :---: |
| 2017-01-03 | 发布版本 v1.0 |

## 联系我

如果你对于本书有什么建议或意见，欢迎批评指正，并联系我。

- [个人主页](http://funhacks.net)
- [GitHub](https://github.com/ethan-funny)
- [Twitter](https://twitter.com/pihacks)

## 支持我

如果你觉得本书对你有所帮助，不妨请我喝杯咖啡，感谢支持！

<img src='https://ofaatpail.qnssl.com/2017-01-03-Ali_Wechat_Pay.png' width='600'>


