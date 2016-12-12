# Mixin 模式

多重继承往往会导致类之间的层次关系变得很复杂，不少编程语言都是不鼓励甚至不支持多重继承。比如，Java 只支持单继承，但为了能达到类似多重继承的效果，提供了接口继承的功能；Ruby 也不支持多继承，但它提供了 Mixin (混入) 的功能，可以将一个模块『混入』到一个类或多个类中，拥有该模块的类就有了该模块的功能。





但是怎么利用多重继承的优点，共享其他类的功能，

Java 只支持单继承，但提供了接口继承的方法，通过继承多个接口，可以显著降低类层次的复杂性。


Mixin 模式可以有效降低多重继承的复杂性


一般把mixin的类放在父类的右边
强调「I can」而不是「I am」







# 参考资料

- [关于Python的Mixin模式 – 思诚之道](http://www.bjhee.com/python-mixin.html)
- [python - What is a mixin, and why are they useful? - Stack Overflow](http://stackoverflow.com/questions/533631/what-is-a-mixin-and-why-are-they-useful)


- [Mixin是什么概念? - 知乎](https://www.zhihu.com/question/20778853)

[Python mixin and MRO - 葱丝瓣酱](http://xiaocong.github.io/blog/2012/06/13/python-mixin-and-mro/)

[python Mixin 扫盲班 - 赖勇浩的编程私伙局 - 博客频道 - CSDN.NET](http://blog.csdn.net/gzlaiyonghao/article/details/1656969)

[为什么Java类只能继承一个父类？ - 知乎](https://www.zhihu.com/question/21476063)

