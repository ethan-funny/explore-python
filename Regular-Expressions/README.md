# 正则表达式

正则表达式（regular expression）是可以匹配文本片段的模式。最简单的正则表达式就是普通字符串，可以匹配其自身。比如，正则表达式 'hello' 可以匹配字符串 'hello'。

要注意的是，正则表达式并不是一个程序，而是用于处理字符串的一种模式，如果你想用它来处理字符串，就必须使用支持正则表达式的工具，比如 Linux 中的 awk, sed, grep，或者编程语言 Perl, Python, Java 等。

正则表达式有多种不同的风格，下表（改编自 [huxi](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)）列出了适用于 Python 或 Perl 等编程语言的部分**元字符**以及说明：

![](https://ofaatpail.qnssl.com/re.png)

# 实例

- 匹配 python.org 的正则表达式：

```
python\.org
```

注：如果使用 `python.org` 来匹配，由于 `.` 可以匹配任意一个字符（换行符除外），因此，它也会匹配到类似 pythonmorg 的字符串，为了匹配点号，我们需要加 `\` 来转义。

- 匹配 010-85692930 的正则表达式：

```
\d{3}\-\d{8}
```

注：`\d` 表示匹配数字，`\d{3}` 表示匹配 3 个数字，`\-` 表示匹配 `-`。

- 匹配由数字、26个英文字母或下划线组成的字符串的正则表达式：

```
^\w+$
```

或

```
^[0-9a-zA-Z_]+$
```

- 匹配 13、15、18 开头的手机号的正则表达式：

```
^(13[0-9]|15[0|1|2|3|5|6|7|8|9]|18[0-9])\d{8}$
```

- 匹配金额，精确到 2 位小数

```
^[0-9]+(.[0-9]{2})?$
```

- 匹配中文的正则表达式：

```python
^[\u4e00-\u9fa5]{0,}$
```

注：[中文的 unicode 编码范围](http://blog.oasisfeng.com/2006/10/19/full-cjk-unicode-range/)主要在 `\u4e00-\u9fa5`。

# 参考资料

- [正则表达式 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)
- [Python正则表达式指南](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)
- [知道这20个正则表达式，能让你少写1,000行代码 - 简书](http://www.jianshu.com/p/e7bb97218946)
- [regexr](http://www.regexr.com/)


