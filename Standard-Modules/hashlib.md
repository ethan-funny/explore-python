# hashlib

Python 内置的 hashlib 模块提供了常见的**摘要算法**（或称哈希算法，散列算法），如 MD5，SHA1, SHA256 等。**摘要算法的基本原理是：将数据（如一段文字）运算变为另一固定长度值**。

MD5 (Message-Digest Algorithm 5, 消息摘要算法），是一种被广泛使用的密码散列函数，可以产生出一个 128 位（16 字节）的散列值（hash value），用于确保信息传输完整一致。

SHA1 (Secure Hash Algorithm, 安全哈希算法) 是 SHA 家族的其中一个算法，它经常被用作数字签名。

# MD5

hashlib 模块提供了 `md5` 函数，我们可以很方便地使用它：

```python
>>> import hashlib
>>>
>>> m = hashlib.md5('md5 test in Python!')
>>> m.digest()
'\xad\xc0\x99\x01\x12\xc7&\xb5~\xb0\xaf \x974\x11\xab'
>>> m.hexdigest()   # 使用一个 32 位的 16 进制字符串表示
'adc0990112c726b57eb0af20973411ab'
```

上面，我们是直接把数据传入 `md5()` 函数，我们也可以通过一次或多次使用 `update` 来实现：

```python
>>> import hashlib
>>> m = hashlib.md5()
>>> m.update('md5 test ')
>>> m.update('in Python!')
>>> m.hexdigest()
'adc0990112c726b57eb0af20973411ab'
```

# SHA1

SHA1 的使用和 MD5 的使用类似：

```python
>>> import hashlib
>>>
>>> sha1 = hashlib.sha1()
>>> sha1.update('md5 test ')
>>> sha1.update('in Python!')
>>> sha1.hexdigest()
'698a8b18d5f99a140520475c342af455183c58a3'
```

# 小结

- MD5 以前经常用来做用户密码的存储。2004年，它被证明无法防止碰撞，因此无法适用于安全性认证，但仍广泛应用于普通数据的错误检查领域。如果你需要储存用户密码，你应该去了解一下诸如 [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2) 或 [bcrypt](https://en.wikipedia.org/wiki/Bcrypt) 之类的算法。而 SHA1 则经常用作数字签名。

# 参考资料

- [MD5 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/MD5)
- [SHA家族 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/SHA%E5%AE%B6%E6%97%8F)


