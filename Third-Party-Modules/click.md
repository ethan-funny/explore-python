# Click

[Click][0] 是 [Flask][1] 的开发团队 [Pallets][2] 的另一款开源项目，它是用于快速创建命令行的第三方模块。我们知道，Python 内置了一个 [Argparse][3] 的标准库用于创建命令行，但使用起来有些繁琐，[Click][0] 相比于 [Argparse][3]，就好比 [requests][4] 相比于 [urllib][5]。

# 快速使用

Click 的使用大致有两个步骤：

1. 使用 `@click.command()` 装饰一个函数，使之成为命令行接口；
2. 使用 `@click.option()` 等装饰函数，为其添加命令行选项等。

它的一种典型使用形式如下：

```python
import click

@click.command()
@click.option('--param', default=default_value, help='description')
def func(param):
    pass
```

下面，让我们看一下[官方文档][6]的入门例子：

```python
import click

@click.command()
@click.option('--count', default=1, help='Number of greetings.')
@click.option('--name', prompt='Your name', help='The person to greet.')
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for x in range(count):
        click.echo('Hello %s!' % name)

if __name__ == '__main__':
    hello()
```

在上面的例子中，函数 `hello` 有两个参数：count 和 name，它们的值从命令行中获取。

- `@click.command()` 使函数 `hello` 成为命令行接口；
- `@click.option` 的第一个参数指定了命令行选项的名称，不难猜到，count 的默认值是 1，name 的值从输入获取；
- 使用 `click.echo` 进行输出是为了获得更好的兼容性，因为 `print` 在 Python2 和 Python3 的用法有些差别。

看看执行情况：

```
$ python hello.py
Your name: Ethan           # 这里会显示 'Your name: '(对应代码中的 prompt)，接受用户输入
Hello Ethan!

$ python hello.py --help   # click 帮我们自动生成了 `--help` 用法
Usage: hello.py [OPTIONS]

  Simple program that greets NAME for a total of COUNT times.

Options:
  --count INTEGER  Number of greetings.
  --name TEXT      The person to greet.
  --help           Show this message and exit.

$ python hello.py --count 3 --name Ethan    # 指定 count 和 name 的值
Hello Ethan!
Hello Ethan!
Hello Ethan!

$ python hello.py --count=3 --name=Ethan    # 也可以使用 `=`，和上面等价
Hello Ethan!
Hello Ethan!
Hello Ethan!

$ python hello.py --name=Ethan              # 没有指定 count，默认值是 1
Hello Ethan!
```

# click.option

option 最基本的用法就是通过指定命令行选项的名称，从命令行读取参数值，再将其传递给函数。在上面的例子，我们看到，除了设置命令行选项的名称，我们还会指定默认值，help 说明等，option 常用的设置参数如下：

- default: 设置命令行参数的默认值
- help: 参数说明
- type: 参数类型，可以是 string, int, float 等
- prompt: 当在命令行中没有输入相应的参数时，会根据 prompt 提示用户输入
- nargs: 指定命令行参数接收的值的个数

下面，我们再看看相关的例子。

## 指定 type

我们可以使用 `type` 来指定参数类型：

```python
import click

@click.command()
@click.option('--rate', type=float, help='rate')   # 指定 rate 是 float 类型
def show(rate):
    click.echo('rate: %s' % rate)

if __name__ == '__main__':
    show()
```

执行情况：

```
$ python click_type.py --rate 1
rate: 1.0
$ python click_type.py --rate 0.66
rate: 0.66
```

## 可选值

在某些情况下，一个参数的值只能是某些可选的值，如果用户输入了其他值，我们应该提示用户输入正确的值。在这种情况下，我们可以通过 `click.Choice()` 来限定：

```python
import click

@click.command()
@click.option('--gender', type=click.Choice(['man', 'woman']))    # 限定值
def choose(gender):
    click.echo('gender: %s' % gender)

if __name__ == '__main__':
    choose()
```

执行情况：

```
$ python click_choice.py --gender boy
Usage: click_choice.py [OPTIONS]

Error: Invalid value for "--gender": invalid choice: boy. (choose from man, woman)

$ python click_choice.py --gender man
gender: man
```

## 多值参数

有时，一个参数需要接收多个值。**option 支持设置固定长度的参数值**，通过 `nargs` 指定。

看看例子就明白了：

```python
import click

@click.command()
@click.option('--center', nargs=2, type=float, help='center of the circle')
@click.option('--radius', type=float, help='radius of the circle')
def circle(center, radius):
    click.echo('center: %s, radius: %s' % (center, radius))

if __name__ == '__main__':
    circle()
```

在上面的例子中，option 指定了两个参数：center 和 radius，其中，center 表示二维平面上一个圆的圆心坐标，接收两个值，以元组的形式将值传递给函数，而 radius 表示圆的半径。

执行情况：

```
$ python click_multi_values.py --center 3 4 --radius 10
center: (3.0, 4.0), radius: 10.0

$ python click_multi_values.py --center 3 4 5 --radius 10
Usage: click_multi_values.py [OPTIONS]

Error: Got unexpected extra argument (5)
```

## 输入密码

有时，在输入密码的时候，我们希望能隐藏显示。option 提供了两个参数来设置密码的输入：hide_input 和 confirmation_promt，其中，hide_input 用于隐藏输入，confirmation_promt 用于重复输入。

看看例子：

```python
import click

@click.command()
@click.option('--password', prompt=True, hide_input=True, confirmation_prompt=True)
def input_password(password):
    click.echo('password: %s' % password)


if __name__ == '__main__':
    input_password()
```

执行情况：

```
$ python click_password.py
Password:                         # 不会显示密码
Repeat for confirmation:          # 重复一遍
password: 666666
```

由于上面的写法有点繁琐，click 也提供了一种快捷的方式，通过使用 `@click.password_option()`，上面的代码可以简写成：

```python
import click

@click.command()
@click.password_option()
def input_password(password):
    click.echo('password: %s' % password)

if __name__ == '__main__':
    input_password()
```

## 改变命令行程序的执行

有些参数会改变命令行程序的执行，比如在终端输入 `python` 是进入 python 控制台，而输入 `python --version` 是打印 python 版本。Click 提供 eager 标识对参数名进行标识，如果输入该参数，则会拦截既定的命令行执行流程，跳转去执行一个回调函数。

让我们看看例子：

```python
import click

def print_version(ctx, param, value):
    if not value or ctx.resilient_parsing:
        return
    click.echo('Version 1.0')
    ctx.exit()

@click.command()
@click.option('--version', is_flag=True, callback=print_version,
              expose_value=False, is_eager=True)
@click.option('--name', default='Ethan', help='name')
def hello(name):
    click.echo('Hello %s!' % name)

if __name__ == '__main__':
    hello()
```

其中：

- `is_eager=True` 表明该命令行选项优先级高于其他选项；
- `expose_value=False` 表示如果没有输入该命令行选项，会执行既定的命令行流程；
- `callback` 指定了输入该命令行选项时，要跳转执行的函数；

执行情况：

```
$ python click_eager.py
Hello Ethan!

$ python click_eager.py --version                   # 拦截既定的命令行执行流程
Version 1.0

$ python click_eager.py --name Michael
Hello Michael!

$ python click_eager.py --version --name Ethan      # 忽略 name 选项
Version 1.0
```

# click.argument

我们除了使用 `@click.option` 来添加**可选参数**，还会经常使用 `@click.argument` 来添加**固定参数**。它的使用和 option 类似，但支持的功能比 option 少。

## 入门使用

下面是一个简单的例子：

```python
import click

@click.command()
@click.argument('coordinates')
def show(coordinates):
    click.echo('coordinates: %s' % coordinates)

if __name__ == '__main__':
    show()
```

看看执行情况：

```click_argument
$ python click_argument.py                     # 错误，缺少参数 coordinates
Usage: click_argument.py [OPTIONS] COORDINATES

Error: Missing argument "coordinates".

$ python click_argument.py --help              # argument 指定的参数在 help 中没有显示
Usage: click_argument.py [OPTIONS] COORDINATES

Options:
  --help  Show this message and exit.

$ python click_argument.py --coordinates 10    # 错误用法，这是 option 参数的用法
Error: no such option: --coordinates

$ python click_argument.py 10                  # 正确，直接输入值即可
coordinates: 10
```

## 多个 argument

我们再来看看多个 argument 的例子：

```python
import click

@click.command()
@click.argument('x')
@click.argument('y')
@click.argument('z')
def show(x, y, z):
    click.echo('x: %s, y: %s, z:%s' % (x, y, z))

if __name__ == '__main__':
    show()
```

执行情况：

```
$ python click_argument.py 10 20 30
x: 10, y: 20, z:30

$ python click_argument.py 10
Usage: click_argument.py [OPTIONS] X Y Z

Error: Missing argument "y".

$ python click_argument.py 10 20
Usage: click_argument.py [OPTIONS] X Y Z

Error: Missing argument "z".

$ python click_argument.py 10 20 30 40
Usage: click_argument.py [OPTIONS] X Y Z

Error: Got unexpected extra argument (40)
```

## 不定参数

argument 还有另外一种常见的用法，就是接收不定量的参数，让我们看看例子：

```python
import click

@click.command()
@click.argument('src', nargs=-1)
@click.argument('dst', nargs=1)
def move(src, dst):
    click.echo('move %s to %s' % (src, dst))

if __name__ == '__main__':
    move()
```

其中，`nargs=-1` 表明参数 `src` 接收不定量的参数值，参数值会以 tuple 的形式传入函数。如果 `nargs` 大于等于 1，表示接收 `nargs` 个参数值，上面的例子中，`dst` 接收一个参数值。

让我们看看执行情况：

```
$ python click_argument.py file1 trash    # src=('file1',)  dst='trash'
move (u'file1',) to trash

$ python click_argument.py file1 file2 file3 trash   # src=('file1', 'file2', 'file3')  dst='trash'
move (u'file1', u'file2', u'file3') to trash
```

# 彩色输出

在前面的例子中，我们使用 `click.echo` 进行输出，如果配合 [colorama][7] 这个模块，我们可以使用 `click.secho` 进行彩色输出，在使用之前，使用 pip 安装 colorama：

```
$ pip install colorama
```

看看例子：

```python
import click

@click.command()
@click.option('--name', help='The person to greet.')
def hello(name):
    click.secho('Hello %s!' % name, fg='red', underline=True)
    click.secho('Hello %s!' % name, fg='yellow', bg='black')

if __name__ == '__main__':
    hello()
```

其中:

- `fg` 表示前景颜色（即字体颜色），可选值有：BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE 等；
- `bg` 表示背景颜色，可选值有：BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE 等；
- `underline` 表示下划线，可选的样式还有：`dim=True`，`bold=True` 等；

# 小结

- 使用 `click.command()` 装饰一个函数，使其成为命令行接口。
- 使用 `click.option()` 添加可选参数，支持设置固定长度的参数值。
- 使用 `click.argument()` 添加固定参数，支持设置不定长度的参数值。

# 参考资料

- [Click Documentation (6.0)](http://click.pocoo.org/6/)
- [Python Click 学习笔记 | I sudo X](https://isudox.com/2016/09/03/learning-python-package-click/)
- [click模块 - cdwanze](http://cdwanze.github.io/%E7%94%B5%E8%84%91/python/%E7%AC%AC%E4%B8%89%E6%96%B9%E6%A8%A1%E5%9D%97/click%E6%A8%A1%E5%9D%97.html)


[0]: https://github.com/pallets/click
[1]: https://github.com/pallets/flask
[2]: https://github.com/pallets
[3]: https://docs.python.org/2/howto/argparse.html
[4]: https://github.com/kennethreitz/requests
[5]: https://docs.python.org/2/library/urllib.html
[6]: http://click.pocoo.org/6/
[7]: https://pypi.python.org/pypi/colorama


