# 读写二进制文件

Python 不仅支持文本文件的读写，也支持二进制文件的读写，比如图片，声音文件等。

# 读取二进制文件

读取二进制文件使用 'rb' 模式。

这里以图片为例：

```python
with open('test.png', 'rb') as f:
    image_data = f.read()    # image_data 是字节字符串格式的，而不是文本字符串
```

这里需要注意的是，在读取二进制数据时，返回的数据是字节字符串格式的，而不是文本字符串。一般情况下，我们可能会对它进行编码，比如 [base64](https://en.wikipedia.org/wiki/Base64) 编码，可以这样做：

```python
import base64

with open('test.png', 'rb') as f:
    image_data = f.read()
    base64_data = base64.b64encode(image_data)    # 使用 base64 编码
    print base64_data
```

下面是执行结果的一部分：

```python
iVBORw0KGgoAAAANSUhEUgAAAQAAAAEACAYAAABccqhmAAAACGFjVEw
```

# 写入二进制文件

写入二进制文件使用 'wb' 模式。

以图片为例：

```python
with open('test.png', 'rb') as f:
    image_data = f.read()

with open('/Users/ethan/test2.png', 'wb') as f:
    f.write(image_data)
```


# 小结

- 读取二进制文件使用 'rb' 模式。
- 写入二进制文件使用 'wb' 模式。

# 参考资料

- [读写字节数据 — python3-cookbook 2.0.0 文档](http://python3-cookbook.readthedocs.io/zh_CN/latest/c05/p04_read_write_binary_data.html)


