# Python 的复活节彩蛋和隐藏的笑话

> 原文：<https://medium.com/hackernoon/pythons-easter-eggs-and-hidden-jokes-d7368c7fe2c2>

## 就在你想的时候，这真是太有趣了。

# 1.你好世界

```
>>> import __hello__
Hello World!
```

# 2.经典

```
>>> import thisThe Zen of Python, by Tim PetersBeautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

# 3.经典中缺失的一行

Python 的禅是在 [PEP 20](https://www.python.org/dev/peps/pep-0020/#id2) 中引入的。它应该是 20 条格言，但只有 19 条被写了下来。

# 4.一堂简单的生活课

```
>>> import this
...
>>> love = this
>>> this is love
True
>>> love is True
False
>>> love is False
False
>>> love is not True or False
True
>>> love is not True or False; love is love  # FML
True
```

# 5.漫画，是的。

```
>>> import antigravity
```

# 6.这不是一个选择，它定义了我们是谁

```
>>> from __future__ import braces
  File "<stdin>", line 1
SyntaxError: not a chance
```

# 7.起源

Python 这个名字和蛇的种类没有关系。

# 8.混乱

这就是`this.py`模块的样子，它打印了 Python 的禅。

```
s = """Gur Mra bs Clguba, ol Gvz CrgrefOrnhgvshy vf orggre guna htyl.
Rkcyvpvg vf orggre guna vzcyvpvg.
Fvzcyr vf orggre guna pbzcyrk.
Pbzcyrk vf orggre guna pbzcyvpngrq.
Syng vf orggre guna arfgrq.
Fcnefr vf orggre guna qrafr.
Ernqnovyvgl pbhagf.
Fcrpvny pnfrf nera'g fcrpvny rabhtu gb oernx gur ehyrf.
Nygubhtu cenpgvpnyvgl orngf chevgl.
Reebef fubhyq arire cnff fvyragyl.
Hayrff rkcyvpvgyl fvyraprq.
Va gur snpr bs nzovthvgl, ershfr gur grzcgngvba gb thrff.
Gurer fubhyq or bar-- naq cersrenoyl bayl bar --boivbhf jnl gb qb vg.
Nygubhtu gung jnl znl abg or boivbhf ng svefg hayrff lbh'er Qhgpu.
Abj vf orggre guna arire.
Nygubhtu arire vf bsgra orggre guna *evtug* abj.
Vs gur vzcyrzragngvba vf uneq gb rkcynva, vg'f n onq vqrn.
Vs gur vzcyrzragngvba vf rnfl gb rkcynva, vg znl or n tbbq vqrn.
Anzrfcnprf ner bar ubaxvat terng vqrn -- yrg'f qb zber bs gubfr!"""d = {}
for c in (65, 97):
    for i in range(26):
        d[chr(i+c)] = chr((i+13) % 26 + c)print("".join([d.get(c, c) for c in s]))
```

禅宗的准则违背了自身。不美而丑，不显而隐。这可能是*唯一一个违背其自身精神的*模块。。

# 9.C/C++有人吗？

又是禅宗，

```
There should be one-- and preferably only one --obvious way to do it.
```

# 10.命名标识符可能会非常酷

就在你认为使用 Python 工作不可能更有趣的时候，

```
>>> from math import pi
>>> π = pi
>>> area = π * r**2>>> résumé = 'knows Python'
>>> 'Python' in résumé
True
```

# 11.为 meetup 挑选一个地方？

```
>>> from antigravity import geohash
>>> # Your location, a date and that date's (or most recent) DJIA opening.
>>> geohash(37.421542, -122.085589, b'2005-05-26-10458.68')
37.857713 -122.544543
```

这可以根据你的位置在 1 个经度和 1 个纬度范围内生成一个 GPS 坐标。

# 12.来自[PEP 401-BDFL 退休](https://www.python.org/dev/peps/pep-0401)的 FLUFL 终身友好语言大叔

```
>>> from __future__ import barry_as_FLUFL
>>> 1 <> 2
True
>>> 1 != 2
  File "<stdin>", line 1
    1 != 2
       ^
SyntaxError: invalid syntax
```

认出来了！= Python 3.0 中的不等式运算符是一个可怕的、令人痛苦的错误，FLUFL 恢复了菱形运算符作为唯一的拼写。

# 13.InPynite？

```
>>> infinity = float('infinity')
>>> hash(infinity)
314159
>>> hash(float('-inf'))
-314159
```

散列是标识特定值的固定大小的整数。仔细一看，无穷大的散列是 10⁵ x 圆周率。有趣的是，hash(float('-inf '))在 python3 中产生-10⁵xπ，而在 python2 中则是-271828 即— 10⁵ x e。

# 笔记

1.  最简单的 hello world 程序，不用调用任何函数
2.  每一行都是 Python 的设计哲学，是至高无上的神圣指南
3.  也许只是为了说明在一个文件的末尾应该总是有一个新的行！
4.  不是复活节彩蛋，是口译员的笑话
5.  它打开了这个 [xkcd 漫画](https://xkcd.com/353),展示了用模块做事情是多么容易
6.  这将立即结束任何关于将花括号引入 Python 的讨论
7.  吉多·范·罗苏姆是巨蟒剧团《飞行马戏团》的超级粉丝
8.  这是一种叫做 [ROT13](https://en.wikipedia.org/wiki/ROT13) 的替代密码
9.  在许多语言中，做同样的事情有两种方法`--no`和`no--`。该消息本身有一个隐藏的例子
10.  Python3 中增加了对命名标识符的 unicode 字符集的支持。尽管在编写代码时，这并不是明确的首选，但它为使用科学公式增添了趣味
11.  原始代码是这里的和引用的 [xkcd 漫画](https://xkcd.com/426/)，也许这就是为什么它也在`antigravity`模块中
12.  [PEP 401](https://www.python.org/dev/peps/pep-0401/) 是愚人节玩笑——PEP 的编号是 401，即 4/01 或 4 月 1 日(愚人节)。PEP 声明吉多·范·罗苏姆即将下台。给他的新头衔将读作“BDEVIL”(仁慈的荣誉退休独裁者，无限期地从语言中休假)，吉多的继任者将是巴里·华沙，或者人们亲切地称他为巴里叔叔。巴里大叔的官方称谓是“FLUFL”(终身友好的语言大叔)。有关于 Parrot 虚拟机和“不存在的”地下 Python 秘密的笑话(可能是对 USENET 上[“TINC】的一个回射)。](https://en.wikipedia.org/wiki/There_Is_No_Cabal)
13.  [来源](https://www.reddit.com/r/Python/comments/6wrd8t/nice_lil_easter_egg_i_suppose/)。

**GitHub:**[https://github.com/orkohunter/python-easter-eggs](https://github.com/orkohunter/python-easter-eggs)