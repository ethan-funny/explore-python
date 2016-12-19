# Base64

**Base64，简单地讲，就是用 64 个字符来表示二进制数据的方法**。这 64 个字符包含小写字母 a-z、大写字母 A-Z、数字 0-9 以及符号"+"、"/"，其实还有一个 "=" 作为后缀用途，所以实际上有 65 个字符。

本文主要介绍如何使用 Python 进行 Base64 编码和解码，关于 Base64 编码转换的规则可以参考 [Base64 笔记](http://www.ruanyifeng.com/blog/2008/06/base64.html)。

Python 内置了一个用于 Base64 编解码的库：`base64`：

- 编码使用 `base64.b64encode()`
- 解码使用 `base64.b64decode()`

下面，我们介绍文本和图片的 Base64 编解码。

# 对文本进行 Base64 编码和解码

```python
>>> import base64
>>> str = 'hello world'
>>>
>>> base64_str = base64.b64encode(str)       # 编码
>>> print base64_str
aGVsbG8gd29ybGQ=
>>>
>>> ori_str = base64.b64decode(base64_str)   # 解码
>>> print ori_str
hello world
```

# 对图片进行 Base64 编码和解码

```python
def convert_image():
    # 原始图片 ==> base64 编码
    with open('/path/to/alpha.png', 'r') as fin:
        image_data = fin.read()
        base64_data = base64.b64encode(image_data)

        fout = open('/path/to/base64_content.txt', 'w')
        fout.write(base64_data)
        fout.close()

    # base64 编码 ==> 原始图片
    with open('/path/to/base64_content.txt', 'r') as fin:
        base64_data = fin.read()
        ori_image_data = base64.b64decode(base64_data)

        fout = open('/path/to/beta.png', 'wb'):
        fout.write(ori_image_data)
        fout.close()
```

# 小结

- Base64 可以将任意二进制数据编码到文本字符串，常用于在 URL、Cookie 和网页中传输少量二进制数据。

# 参考资料

- [Base64 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/Base64)
- [Base64笔记 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2008/06/base64.html)


