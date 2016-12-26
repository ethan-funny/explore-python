# hmac

HMAC 是用于消息认证的加密哈希算法，全称是 keyed-Hash Message Authentication Code。**HMAC 利用哈希算法，以一个密钥和一个消息作为输入，生成一个加密串作为输出**。HMAC 可以有效防止类似 MD5 的彩虹表等攻击，比如将常见密码的 MD5 值存入数据库，可能被反向破解。

Python 的 `hmac` 模块提供了 HMAC 算法，它的使用形式是：

```python
hmac.new(key[, msg[, digestmod]])
```

其中，key 是一个密钥；msg 是消息，可选，如果给出 msg，则调用方法 `update(msg)`；digestmod 是 HMAC 对象使用的摘要构造函数或模块，默认为 `hashlib.md5` 构造函数。

HMAC 对象常用的方法有：

- HMAC.update(msg)

用字符串 msg 更新 HMAC 对象，重复的调用等同于一次调用所有参数的组合，即：

```
m.update(a);
m.update(b);
```

相当于

```
m.update(a+b)
```

- HMAC.digest()

返回目前传递给 `update()` 方法的字符串的摘要。此字符串长度将与给构造函数的摘要的 digest_size 相同。它可能包含非 ASCII 字符，包括 NULL 字节。

- HMAC.hexdigest()

类似 digest()，但是返回的摘要的字符串的长度翻倍，且只包含十六进制数字。

现在，让我们看一个简单的例子：

```python
>>> from datetime import datetime
>>> import hashlib
>>> import hmac

>>> key = 'you-never-know'
>>> msg = datetime.utcnow().strftime('%Y-%m-%d')

>>> m = hmac.new(key, msg, hashlib.sha1)
>>> signature = m.hexdigest()
>>> signature
'fdb2087a66a2f00afbc1884738467ba089782779'
```

# 参考资料

- [hmac — 用于消息认证的加密哈希算法](http://python.usyiyi.cn/python_278/library/hmac.html)


