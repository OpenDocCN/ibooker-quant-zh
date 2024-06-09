# 第三章：数据类型和结构

> 糟糕的程序员担心代码。优秀的程序员担心数据结构及其关系。
> 
> Linus Torvalds

# 介绍

本章介绍了`Python`的基本数据类型和数据结构。尽管`Python`解释器本身已经带来了丰富多样的数据结构，但`NumPy`和其他库在其中增添了宝贵的内容。

本章组织如下：

“基本数据类型”

第一节介绍了基本数据类型，如`int`、`float`和`string`。

“基本数据结构”

下一节介绍了`Python`的基本数据结构（例如`list`对象）并说明了控制结构、函数式编程范式和匿名函数。

本章的精神是在涉及数据类型和结构时提供对`Python`特定内容的一般介绍。如果您具备来自其他编程语言（例如`C`或`Matlab`）的背景，那么您应该能够轻松掌握`Python`用法可能带来的差异。此处介绍的主题对于接下来的章节都是重要且基础的。

本章涵盖以下数据类型和结构：

| 对象类型 | 含义 | 用法/模型 |
| --- | --- | --- |
| `int` | 整数值 | 自然数 |
| `float` | 浮点数 | 实数 |
| `bool` | 布尔值 | 某种真或假 |
| `str` | 字符串对象 | 字符、单词、文本 |
| `tuple` | 不可变容器 | 固定的对象集合、记录 |
| `list` | 可变容器 | 变化的对象集合 |
| `dict` | 可变容器 | 键-值存储 |
| `set` | 可变容器 | 唯一对象的集合 |

# 基本数据类型

`Python` 是一种*动态类型*语言，这意味着`Python`解释器在运行时推断对象的类型。相比之下，像`C`这样的编译语言通常是*静态类型*的。在这些情况下，对象的类型必须在编译时与对象关联。¹

## 整数

最基本的数据类型之一是整数，或者``int``：

```py
In [1]: a = 10
        type(a)
Out[1]: int
```

内置函数`type`提供了标准和内置类型的所有对象的类型信息，以及新创建的类和对象。在后一种情况下，提供的信息取决于程序员与类存储的描述。有一句话说“`Python`中的一切都是对象。”这意味着，例如，即使是我们刚刚定义的`int`对象这样的简单对象也有内置方法。例如，您可以通过调用方法`bit_length`来获取表示内存中的`int`对象所需的位数：

```py
In [2]: a.bit_length()
Out[2]: 4
```

您会发现，所需的位数随我们分配给对象的整数值的增加而增加：

```py
In [3]: a = 100000
        a.bit_length()
Out[3]: 17
```

一般来说，有很多不同的方法，很难记住所有类和对象的所有方法。高级`Python`环境，如`IPython`，提供了可以显示附加到对象的所有方法的制表符完成功能。您只需键入对象名称，然后跟一个点（例如，`a.`），然后按 Tab 键，例如，`a.*tab*`。然后，这将提供您可以调用的对象的方法集合。或者，`Python`内置函数`dir`提供了任何对象的完整属性和方法列表。

`Python`的一个特殊之处在于整数可以是任意大的。例如，考虑谷歌数 10¹⁰⁰。`Python`对于这样的大数没有问题，这些大数在技术上是`long`对象：

```py
In [4]: googol = 10 ** 100
        googol
Out[4]: 10000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

In [5]: googol.bit_length()
Out[5]: 333
```

# 大整数

`Python` 整数可以是任意大的。解释器会根据需要使用尽可能多的位/字节来表示数字。

整数的算术运算易于实现：

```py
In [6]: 1 + 4
Out[6]: 5

In [7]: 1 / 4
Out[7]: 0.25

In [8]: type(1 / 4)
Out[8]: float
```

## 浮点数

最后一个表达式返回通常*期望*的结果 0.25（在基本的 Python 2.7 中不同）。这使我们进入了下一个基本数据类型，即`float`对象。在整数值后添加一个点，如`1.`或`1.0`，会导致`Python`将对象解释为`float`。涉及`float`的表达式通常也返回一个`float`对象：^（2）

```py
In [9]: 1.6 / 4
Out[9]: 0.4

In [10]: type (1.6 / 4)
Out[10]: float
```

`float`稍微复杂一些，因为有理数或实数的计算机化表示通常不是精确的，而是取决于所采取的具体技术方法。为了说明这意味着什么，让我们定义另一个`float`对象`b`。像这样的`float`对象总是内部表示为仅具有一定精度的。当向`b`添加 0.1 时，这变得明显：

```py
In [11]: b = 0.35
         type(b)
Out[11]: float

In [12]: b + 0.1
Out[12]: 0.44999999999999996
```

这是因为+`float`+s 在内部以二进制格式表示；也就是说，十进制数<math alttext="0 less-than n less-than 1"><mrow><mn>0</mn> <mo><</mo> <mi>n</mi> <mo><</mo> <mn>1</mn></mrow></math>通过形式为<math alttext="n equals StartFraction x Over 2 EndFraction plus StartFraction y Over 4 EndFraction plus StartFraction z Over 8 EndFraction plus period period period"><mrow><mi>n</mi> <mo>=</mo> <mfrac><mi>x</mi> <mn>2</mn></mfrac> <mo>+</mo> <mfrac><mi>y</mi> <mn>4</mn></mfrac> <mo>+</mo> <mfrac><mi>z</mi> <mn>8</mn></mfrac> <mo>+</mo> <mo>.</mo> <mo>.</mo> <mo>.</mo></mrow></math>的系列表示。对于某些浮点数，二进制表示可能涉及大量元素，甚至可能是一个无限级数。然而，给定用于表示此类数字的固定位数-即表示系列中的固定项数-不准确是其结果。其他数字可以*完美*表示，因此即使只有有限数量的位可用，它们也会被精确地存储。考虑以下示例：

```py
In [13]: c = 0.5
         c.as_integer_ratio()
Out[13]: (1, 2)
```

一半，即 0.5，被准确地存储，因为它有一个精确（有限）的二进制表示：<math alttext="normal dollar-sign 0.5 equals one-half normal dollar-sign"><mrow><mi>$</mi> <mn>0</mn> <mo>.</mo> <mn>5</mn> <mo>=</mo> <mfrac><mn>1</mn> <mn>2</mn></mfrac> <mi>$</mi></mrow></math> 。然而，对于`b = 0.35`，我们得到的结果与期望的有理数<math alttext="normal dollar-sign 0.35 equals seven-twenty-ths normal dollar-sign"><mrow><mi>$</mi> <mn>0</mn> <mo>.</mo> <mn>35</mn> <mo>=</mo> <mfrac><mn>7</mn> <mn>20</mn></mfrac> <mi>$</mi></mrow></math> 不同：

```py
In [14]: b.as_integer_ratio()
Out[14]: (3152519739159347, 9007199254740992)
```

精度取决于用于表示数字的位数。一般来说，所有`Python`运行的平台都使用 IEEE 754 双精度标准（即 64 位）来进行内部表示。³ 这意味着相对精度为 15 位数字。

由于这个主题在金融等几个应用领域非常重要，有时需要确保数字的确切或至少是最佳可能的表示。例如，在对一大堆数字求和时，这个问题可能很重要。在这种情况下，某种类型和/或数量级别的表示误差可能导致与基准值的显著偏差。

模块`decimal`提供了一个用于浮点数的任意精度对象，以及几个选项来解决使用这些数字时的精度问题：

```py
In [15]: import decimal
         from decimal import Decimal

In [16]: decimal.getcontext()
Out[16]: Context(prec=28, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999, capitals=1, clamp=0, flags=[], traps=[InvalidOperation, DivisionByZero, Overflow])

In [17]: d = Decimal(1) / Decimal (11)
         d
Out[17]: Decimal('0.09090909090909090909090909091')
```

您可以通过改变`Context`对象的相应属性值来改变表示的精度：

```py
In [18]: decimal.getcontext().prec = 4  # ①

In [19]: e = Decimal(1) / Decimal (11)
         e
Out[19]: Decimal('0.09091')

In [20]: decimal.getcontext().prec = 50  # ②

In [21]: f = Decimal(1) / Decimal (11)
         f
Out[21]: Decimal('0.090909090909090909090909090909090909090909090909091')
```

①

低于默认精度。

②

高于默认精度。

如有需要，可以通过这种方式调整精度以适应手头的确切问题，并且可以处理具有不同精度的浮点对象：

```py
In [22]: g = d + e + f
         g
Out[22]: Decimal('0.27272818181818181818181818181909090909090909090909')
```

# 任意精度浮点数

模块`decimal`提供了一个任意精度浮点数对象。在金融中，有时需要确保高精度并超越 64 位双精度标准。

## 布尔值

在编程中，评估比较或逻辑表达式，例如`4 > 3`，`4.5 <= 3.25`或`(4 > 3) and (3 > 2)`，会产生`True`或`False`之一作为输出，这是两个重要的 Python 关键字。其他例如`def`，`for`或`if`。Python 关键字的完整列表可在`keyword`模块中找到。

```py
In [23]: import keyword

In [24]: keyword.kwlist
Out[24]: ['False',
          'None',
          'True',
          'and',
          'as',
          'assert',
          'break',
          'class',
          'continue',
          'def',
          'del',
          'elif',
          'else',
          'except',
          'finally',
          'for',
          'from',
          'global',
          'if',
          'import',
          'in',
          'is',
          'lambda',
          'nonlocal',
          'not',
          'or',
          'pass',
          'raise',
          'return',
          'try',
          'while',
          'with',
          'yield']
```

`True`和`False`是`bool`数据类型，代表布尔值。以下代码展示了 Python 对相同操作数应用*比较*操作符后生成的`bool`对象。

```py
In [25]: 4 > 3  # ①
Out[25]: True

In [26]: type(4 > 3)
Out[26]: bool

In [27]: type(False)
Out[27]: bool

In [28]: 4 >= 3  # ②
Out[28]: True

In [29]: 4 < 3  # ③
Out[29]: False

In [30]: 4 <= 3  # ④
Out[30]: False

In [31]: 4 == 3  # ⑤
Out[31]: False

In [32]: 4 != 3  # ⑥
Out[32]: True
```

①

更大。

②

大于或等于。

③

更小。

④

小于或等于。

⑤

相等。

⑥

不相等。

经常，*逻辑*运算符被应用于`bool`对象，从而产生另一个`bool`对象。

```py
In [33]: True and True
Out[33]: True

In [34]: True and False
Out[34]: False

In [35]: False and False
Out[35]: False

In [36]: True or True
Out[36]: True

In [37]: True or False
Out[37]: True

In [38]: False or False
Out[38]: False

In [39]: not True
Out[39]: False

In [40]: not False
Out[40]: True
```

当然，这两种类型的运算符经常结合使用。

```py
In [41]: (4 > 3) and (2 > 3)
Out[41]: False

In [42]: (4 == 3) or (2 != 3)
Out[42]: True

In [43]: not (4 != 4)
Out[43]: True

In [44]: (not (4 != 4)) and (2 == 3)
Out[44]: False
```

其一主要应用是通过其他 Python 关键字（例如`if`或`while`）来控制代码流程（本章后面将有更多示例）。

```py
In [45]: if 4 > 3:  # ①
             print('condition true')  # ②

         condition true

In [46]: i = 0  # ③
         while i < 4:  # ④
             print('condition true, i = ', i)  # ⑤
             i += 1  # ⑥

         condition true, i =  0
         condition true, i =  1
         condition true, i =  2
         condition true, i =  3
```

①

如果条件成立，则执行要跟随的代码。

②

如果条件成立，则执行要跟随的代码。

③

使用 0 初始化参数`i`。

④

只要条件成立，就执行并重复执行后续代码。

⑤

打印文本和参数`i`的值。

⑥

将参数值增加 1；`i += 1`等同于`i = i + 1`。

在数值上，Python 将`False`赋值为 0，将`True`赋值为 1。通过`bool()`函数将数字转换为`bool`对象时，0 给出`False`，而所有其他数字给出`True`。

```py
In [47]: int(True)
Out[47]: 1

In [48]: int(False)
Out[48]: 0

In [49]: float(True)
Out[49]: 1.0

In [50]: float(False)
Out[50]: 0.0

In [51]: bool(0)
Out[51]: False

In [52]: bool(0.0)
Out[52]: False

In [53]: bool(1)
Out[53]: True

In [54]: bool(10.5)
Out[54]: True

In [55]: bool(-2)
Out[55]: True
```

## 字符串

现在我们可以表示自然数和浮点数，我们转向文本。在`Python`中表示文本的基本数据类型是`string`。``string``对象具有许多非常有用的内置方法。事实上，当涉及到处理任何类型和任何大小的文本文件时，`Python`通常被认为是一个很好的选择。`string`对象通常由单引号或双引号定义，或者通过使用`str`函数转换另一个对象（即使用对象的标准或用户定义的`string`表示）来定义：

```py
In [56]: t = 'this is a string object'
```

关于内置方法，例如，您可以将对象中的第一个单词大写：

```py
In [57]: t.capitalize()
Out[57]: 'This is a string object'
```

或者您可以将其拆分为其单词组件以获得所有单词的`list`对象（稍后会有关于`list`对象的更多内容）：

```py
In [58]: t.split()
Out[58]: ['this', 'is', 'a', 'string', 'object']
```

您还可以搜索单词并在成功情况下获得单词的第一个字母的位置（即，索引值）：

```py
In [59]: t.find('string')
Out[59]: 10
```

如果单词不在`string`对象中，则该方法返回-1：

```py
In [60]: t.find('Python')
Out[60]: -1
```

用`replace`方法轻松替换字符串中的字符是一个典型的任务：

```py
In [61]: t.replace(' ', '|')
Out[61]: 'this|is|a|string|object'
```

字符串的剥离—即删除某些前导/后置字符—也经常是必要的：

```py
In [62]: 'http://www.python.org'.strip('htp:/')
Out[62]: 'www.python.org'
```

表格 3-1 列出了`string`对象的许多有用方法。

表格 3-1\. 选择的字符串方法

| 方法 | 参数 | 返回/结果 |
| --- | --- | --- |
| `capitalize` | `()` | 第一个字母大写的字符串副本 |
| `count` | `(子串[, 开始[, 结束]])` | 子串出现次数的计数 |
| `decode` | `([编码[, 错误]])` | 使用`编码`（例如，UTF-8）的字符串的解码版本 |
| `encode` | `([编码+[, 错误]])` | 字符串的编码版本 |
| `find` | `(sub[, start[, end]])` | 找到子字符串的（最低）索引 |
| `join` | `(seq)` | 将序列`seq`中的字符串连接起来 |
| `replace` | `(old, new[, count])` | 将`old`替换为`new`的第一个`count`次 |
| `split` | `([sep[, maxsplit]])` | 以`sep`为分隔符的字符串中的单词列表 |
| `splitlines` | `([keepends])` | 如果`keepends`为`True`，则带有行结束符/断行的分隔行 |
| `strip` | `(chars)` | 删除`chars`中的前导/尾随字符的字符串的副本 |
| `upper` | `()` | 复制并将所有字母大写 |

###### 注意

从 Python 2.7（本书的第一版）到 Python 3.6（本书的第二版使用的版本）的基本变化是字符串对象的编码和解码以及 Unicode 的引入（参见[*https://docs.python.org/3/howto/unicode.html*](https://docs.python.org/3/howto/unicode.html)）。本章不允许详细讨论此上下文中重要的许多细节。对于本书的目的，主要涉及包含英文单词的数字数据和标准字符串，这种省略似乎是合理的。

## 附录：打印和字符串替换

打印`str`对象或其他 Python 对象的字符串表示通常是通过`print()`函数完成的（在 Python 2.7 中是一个语句）。

```py
In [63]: print('Python for Finance')  # ①

         Python for Finance

In [64]: print(t)  # ②

         this is a string object

In [65]: i = 0
         while i < 4:
             print(i)  # ③
             i += 1

         0
         1
         2
         3

In [66]: i = 0
         while i < 4:
             print(i, end='|')  # ④
             i += 1

         0|1|2|3|
```

①

打印一个`str`对象。

②

打印由变量名引用的`str`对象。

③

打印`int`对象的字符串表示。

④

指定打印的最后一个字符（默认为前面看到的换行符`\n`）。

Python 提供了强大的字符串替换操作。有通过`%`字符进行的旧方法和通过花括号`{}`和`format()`进行的新方法。两者在实践中仍然适用。本节不能提供所有选项的详尽说明，但以下代码片段显示了一些重要的内容。首先，*旧*的方法。

```py
In [67]: 'this is an integer %d' % 15  # ①
Out[67]: 'this is an integer 15'

In [68]: 'this is an integer %4d' % 15  # ②
Out[68]: 'this is an integer   15'

In [69]: 'this is an integer %04d' % 15  # ③
Out[69]: 'this is an integer 0015'

In [70]: 'this is a float %f' % 15.3456  # ④
Out[70]: 'this is a float 15.345600'

In [71]: 'this is a float %.2f' % 15.3456  # ⑤
Out[71]: 'this is a float 15.35'

In [72]: 'this is a float %8f' % 15.3456  # ⑥
Out[72]: 'this is a float 15.345600'

In [73]: 'this is a float %8.2f' % 15.3456  # ⑦
Out[73]: 'this is a float    15.35'

In [74]: 'this is a float %08.2f' % 15.3456  # ⑧
Out[74]: 'this is a float 00015.35'

In [75]: 'this is a string %s' % 'Python'  # ⑨
Out[75]: 'this is a string Python'

In [76]: 'this is a string %10s' % 'Python'  # ⑩
Out[76]: 'this is a string     Python'
```

①

`int`对象替换。

②

带有固定数量的字符。

③

如果必要，带有前导零。

④

`float`对象替换。

⑤

带有固定数量的小数位数。

⑥

带有固定数量的字符（并填充小数）。

⑦

带有固定数量的字符和小数位数…

⑧

… 以及必要时的前导零。

⑨

`str`对象替换。

⑩

带有固定数量的字符。

现在以*新*方式实现相同的示例。请注意，输出在某些地方略有不同。

```py
In [77]: 'this is an integer {:d}'.format(15)
Out[77]: 'this is an integer 15'

In [78]: 'this is an integer {:4d}'.format(15)
Out[78]: 'this is an integer   15'

In [79]: 'this is an integer {:04d}'.format(15)
Out[79]: 'this is an integer 0015'

In [80]: 'this is a float {:f}'.format(15.3456)
Out[80]: 'this is a float 15.345600'

In [81]: 'this is a float {:.2f}'.format(15.3456)
Out[81]: 'this is a float 15.35'

In [82]: 'this is a float {:8f}'.format(15.3456)
Out[82]: 'this is a float 15.345600'

In [83]: 'this is a float {:8.2f}'.format(15.3456)
Out[83]: 'this is a float    15.35'

In [84]: 'this is a float {:08.2f}'.format(15.3456)
Out[84]: 'this is a float 00015.35'

In [85]: 'this is a string {:s}'.format('Python')
Out[85]: 'this is a string Python'

In [86]: 'this is a string {:10s}'.format('Python')
Out[86]: 'this is a string Python    '
```

字符串替换在多次打印操作的上下文中特别有用，其中打印的数据会更新，例如，在`while`循环期间。

```py
In [87]: i = 0
         while i < 4:
             print('the number is %d' % i)
             i += 1

         the number is 0
         the number is 1
         the number is 2
         the number is 3

In [88]: i = 0
         while i < 4:
             print('the number is {:d}'.format(i))
             i += 1

         the number is 0
         the number is 1
         the number is 2
         the number is 3
```

## 旅行：正则表达式

在处理`string`对象时，使用*正则表达式*是一种强大的工具。`Python`在模块`re`中提供了这样的功能：

```py
In [89]: import re
```

假设你面对一个大文本文件，例如一个逗号分隔值（`CSV`）文件，其中包含某些时间序列和相应的日期时间信息。往往情况下，日期时间信息以`Python`无法直接解释的格式提供。然而，日期时间信息通常可以通过正则表达式描述。考虑以下`string`对象，其中包含三个日期时间元素，三个整数和三个字符串。请注意，三重引号允许在多行上定义字符串：

```py
In [90]: series = """
 '01/18/2014 13:00:00', 100, '1st';
 '01/18/2014 13:30:00', 110, '2nd';
 '01/18/2014 14:00:00', 120, '3rd'
 """
```

以下正则表达式描述了提供在`string`对象中的日期时间信息的格式：⁴

```py
In [91]: dt = re.compile("'[0-9/:\s]+'")  # datetime
```

有了这个正则表达式，我们可以继续找到所有日期时间元素。通常，将正则表达式应用于`string`对象还会导致典型解析任务的性能改进：

```py
In [92]: result = dt.findall(series)
         result
Out[92]: ["'01/18/2014 13:00:00'", "'01/18/2014 13:30:00'", "'01/18/2014 14:00:00'"]
```

# 正则表达式

在解析`string`对象时，考虑使用正则表达式，这可以为此类操作带来便利性和性能。

然后可以解析生成`Python datetime`对象的结果`string`对象（参见[Link to Come]，了解如何使用`Python`处理日期和时间数据的概述）。要解析包含日期时间信息的`string`对象，我们需要提供如何解析的信息 —— 再次作为`string`对象：

```py
In [93]: from datetime import datetime
         pydt = datetime.strptime(result[0].replace("'", ""),
                                  '%m/%d/%Y %H:%M:%S')
         pydt
Out[93]: datetime.datetime(2014, 1, 18, 13, 0)

In [94]: print(pydt)

         2014-01-18 13:00:00

In [95]: print(type(pydt))

         <class 'datetime.datetime'>
```

后续章节将提供有关日期时间数据的更多信息，以及处理此类数据和`datetime`对象及其方法。这只是对金融中这一重要主题的一个引子。

# 基本数据结构

作为一个通用规则，数据结构是包含可能大量其他对象的对象。在`Python`提供的内置结构中包括：

`tuple`

一个不可变的任意对象的集合；只有少量方法可用

`list`

一个可变的任意对象的集合；有许多方法可用

`dict`

键-值存储对象

`set`

用于其他*唯一*对象的无序集合对象

## 元组

`tuple`是一种高级数据结构，但在其应用中仍然相当简单且有限。通过在括号中提供对象来定义它：

```py
In [96]: t = (1, 2.5, 'data')
         type(t)
Out[96]: tuple
```

你甚至可以放弃括号，提供多个对象，只需用逗号分隔：

```py
In [97]: t = 1, 2.5, 'data'
         type(t)
Out[97]: tuple
```

像几乎所有的`Python`数据结构一样，``tuple``具有内置索引，借助它可以检索单个或多个`tuple`元素。重要的是要记住，`Python`使用*零基编号*，因此`tuple`的第三个元素位于索引位置 2：

```py
In [98]: t[2]
Out[98]: 'data'

In [99]: type(t[2])
Out[99]: str
```

# 零基编号

与其他一些编程语言（如`Matlab`）相比，`Python`使用零基编号方案。例如，`tuple`对象的第一个元素的索引值为 0。

这种对象类型提供的特殊方法仅有两个：`count`和`index`。第一个方法统计某个对象的出现次数，第二个方法给出其第一次出现的索引值：

```py
In [100]: t.count('data')
Out[100]: 1

In [101]: t.index(1)
Out[101]: 0
```

`tuple`对象是*不可变*对象。这意味着一旦定义，它们就不容易更改。

## 列表

类型为`list`的对象比`tuple`对象更加灵活和强大。从财务角度来看，你可以仅使用`list`对象就能实现很多，比如存储股价报价和添加新数据。`list`对象通过括号定义，其基本功能和行为与`tuple`对象相似：

```py
In [102]: l = [1, 2.5, 'data']
          l[2]
Out[102]: 'data'
```

也可以通过使用函数`list`来定义或转换`list`对象。以下代码通过转换前面示例中的`tuple`对象生成一个新的`list`对象：

```py
In [103]: l = list(t)
          l
Out[103]: [1, 2.5, 'data']

In [104]: type(l)
Out[104]: list
```

除了`tuple`对象的特性外，``list``对象还可以通过不同的方法进行扩展和缩减。换句话说，虽然`string`和`tuple`对象是*不可变*序列对象（具有索引），一旦创建就无法更改，但`list`对象是*可变*的，并且可以通过不同的操作进行更改。你可以将`list`对象附加到现有的`list`对象上，等等：

```py
In [105]: l.append([4, 3])  # ①
          l
Out[105]: [1, 2.5, 'data', [4, 3]]

In [106]: l.extend([1.0, 1.5, 2.0])  # ②
          l
Out[106]: [1, 2.5, 'data', [4, 3], 1.0, 1.5, 2.0]

In [107]: l.insert(1, 'insert')  # ③
          l
Out[107]: [1, 'insert', 2.5, 'data', [4, 3], 1.0, 1.5, 2.0]

In [108]: l.remove('data')  # ④
          l
Out[108]: [1, 'insert', 2.5, [4, 3], 1.0, 1.5, 2.0]

In [109]: p = l.pop(3)  # ⑤
          print(l, p)

          [1, 'insert', 2.5, 1.0, 1.5, 2.0] [4, 3]
```

①

在末尾附加`list`对象。

②

添加`list`对象的元素。

③

在索引位置之前插入对象。

④

删除对象的第一次出现。

⑤

删除并返回索引位置的对象。

切片也很容易实现。在这里，*切片*指的是将数据集分解为较小部分（感兴趣的部分）的操作：

```py
In [110]: l[2:5]  # ①
Out[110]: [2.5, 1.0, 1.5]
```

①

第三到第五个元素。

Table 3-2 提供了`list`对象的选定操作和方法的摘要。

表 3-2\. `list`对象的选定操作和方法

| 方法 | 参数 | 返回/结果 |
| --- | --- | --- |
| `l[i] = x` | `[i]` | 将第`i`个元素替换为`x` |
| `l[i:j:k] = s` | `[i:j:k]` | 用`s`替换从`i`到`j-1`的每个第`k`个元素 |
| `append` | `(x)` | 将`x`附加到对象 |
| `count` | `(x)` | 对象`x`的出现次数 |
| `del l[i:j:k]` | `[i:j:k]` | 删除索引值为`i`到`j-1`的元素 |
| `extend` | `(s)` | 将`s`的所有元素附加到对象 |
| `index` | `(x[, i[, j]])` | 元素`i`和`j-1`之间`x`的第一个索引 |
| `insert` | `(i, x)` | 在索引`i`之前/后插入`x` |
| `remove` | `(i)` | 删除索引为`i`的元素 |
| `pop` | `(i)` | 删除索引为`i`的元素并返回它 |
| `reverse` | `()` | 将所有项目原地颠倒 |
| `sort` | `([cmp[, key[, reverse]]])` | 原地对所有项目排序 |

## 专题：控制结构

虽然控制结构本身是一个专题，像`for`循环这样的*控制结构*可能最好是基于`Python`中的`list`对象介绍的。这是因为一般情况下循环是在`list`对象上进行的，这与其他语言中通常的标准相当不同。看下面的例子。`for`循环遍历`list`对象`l`的元素，索引值为 2 到 4，并打印出相应元素的平方。注意第二行缩进（空格）的重要性：

```py
In [111]: for element in l[2:5]:
              print(element ** 2)

          6.25
          1.0
          2.25
```

这相比于典型的基于计数器的循环提供了非常高的灵活性。基于（标准的）`list`对象`range`也可以使用计数器进行循环：

```py
In [112]: r = range(0, 8, 1)  # ①
          r
Out[112]: range(0, 8)

In [113]: type(r)
Out[113]: range
```

①

参数是`start`、`end`、`step size`。

为了比较，相同的循环使用`range`实现如下：

```py
In [114]: for i in range(2, 5):
              print(l[i] ** 2)

          6.25
          1.0
          2.25
```

# 遍历列表

在`Python`中，你可以遍历任意的`list`对象，不管对象的内容是什么。这通常避免了引入计数器。

`Python`还提供了典型的（条件）控制元素`if`、`elif`和`else`。它们在其他语言中的使用方式类似：

```py
In [115]: for i in range(1, 10):
              if i % 2 == 0:  # ①
                  print("%d is even" % i)
              elif i % 3 == 0:
                  print("%d is multiple of 3" % i)
              else:
                  print("%d is odd" % i)

          1 is odd
          2 is even
          3 is multiple of 3
          4 is even
          5 is odd
          6 is even
          7 is odd
          8 is even
          9 is multiple of 3
```

①

`%`代表取模。

同样，`while`提供了另一种控制流的手段：

```py
In [116]: total = 0
          while total < 100:
              total += 1
          print(total)

          100
```

`Python`的一个特点是所谓的`list` *推导式*。与遍历现有`list`对象不同，这种方法以一种相当紧凑的方式通过循环生成`list`对象：

```py
In [117]: m = [i ** 2 for i in range(5)]
          m
Out[117]: [0, 1, 4, 9, 16]
```

从某种意义上说，这已经提供了一种生成“类似”的向量化代码的第一手段，因为循环相对来说更加隐式而不是显式的（代码的向量化将在本章后面更详细地讨论）。

## 专题：功能编程

`Python`还提供了一些功能编程支持工具，即将函数应用于整套输入（在我们的情况下是`list`对象）。其中包括`filter`、`map`和`reduce`。然而，我们首先需要一个函数定义。首先从一个非常简单的函数开始，考虑一个返回输入`x`的平方的函数`f`：

```py
In [118]: def f(x):
              return x ** 2
          f(2)
Out[118]: 4
```

当然，函数可以是任意复杂的，具有多个输入/参数对象，甚至多个输出（返回对象）。但是，考虑以下函数：

```py
In [119]: def even(x):
              return x % 2 == 0
          even(3)
Out[119]: False
```

返回对象是一个布尔值。这样的函数可以通过使用 `map` 应用于整个 `list` 对象：

```py
In [120]: list(map(even, range(10)))
Out[120]: [True, False, True, False, True, False, True, False, True, False]
```

为此，我们还可以直接将函数定义作为 `map` 的参数提供，通过使用 `lambda` 或*匿名*函数：

```py
In [121]: list(map(lambda x: x ** 2, range(10)))
Out[121]: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

函数也可以用于过滤 `list` 对象。在下面的示例中，过滤器返回符合由 `even` 函数定义的布尔条件的 `list` 对象元素：

```py
In [122]: list(filter(even, range(15)))
Out[122]: [0, 2, 4, 6, 8, 10, 12, 14]
```

# 列表推导、函数式编程、匿名函数

在 `Python` 级别尽可能避免使用循环被认为是*良好的实践*。`list` 推导和函数式编程工具如 `map`、`filter` 和 `reduce` 提供了编写没有（显式）循环的代码的方法，这种代码既紧凑又通常更可读。在这种情况下，`lambda` 或匿名函数也是强大的工具。

## 字典

`dict` 对象是字典，也是可变序列，允许通过可以是 `string` 对象的键来检索数据。它们被称为*键值存储*。虽然 `list` 对象是有序且可排序的，但 `dict` 对象是无序且不可排序的。通过示例可以更好地说明与 `list` 对象的进一步差异。花括号是定义 `dict` 对象的标志：

```py
In [123]: d = {
               'Name' : 'Angela Merkel',
               'Country' : 'Germany',
               'Profession' : 'Chancelor',
               'Age' : 63
               }
          type(d)
Out[123]: dict

In [124]: print(d['Name'], d['Age'])

          Angela Merkel 63
```

同样，这类对象具有许多内置方法：

```py
In [125]: d.keys()
Out[125]: dict_keys(['Name', 'Country', 'Profession', 'Age'])

In [126]: d.values()
Out[126]: dict_values(['Angela Merkel', 'Germany', 'Chancelor', 63])

In [127]: d.items()
Out[127]: dict_items([('Name', 'Angela Merkel'), ('Country', 'Germany'), ('Profession', 'Chancelor'), ('Age', 63)])

In [128]: birthday = True
          if birthday is True:
              d['Age'] += 1
          print(d['Age'])

          64
```

有几种方法可以从 `dict` 对象获取 `iterator` 对象。当进行迭代时，这些对象的行为类似于 `list` 对象：

```py
In [129]: for item in d.items():
              print(item)

          ('Name', 'Angela Merkel')
          ('Country', 'Germany')
          ('Profession', 'Chancelor')
          ('Age', 64)

In [130]: for value in d.values():
              print(type(value))

          <class 'str'>
          <class 'str'>
          <class 'str'>
          <class 'int'>
```

表 3-3 提供了 `dict` 对象的选定操作和方法的摘要。

表 3-3\. `dict` 对象的选定操作和方法

| 方法 | 参数 | 返回/结果 |
| --- | --- | --- |
| `d[k]` | `[k]` | `d` 中具有键 `k` 的项目 |
| `d[k] = x` | `[k]` | 将项目键 `k` 设置为 `x` |
| `del d[k]` | `[k]` | 删除具有键 `k` 的项目 |
| `clear` | `()` | 删除所有项目 |
| `copy` | `()` | 复制一个副本 |
| `has_key` | `(k)` | 如果 `k` 是键，则为 `True` |
| `items` | `()` | 迭代器遍历所有项目 |
| `keys` | `()` | 迭代器遍历所有键 |
| `values` | `()` | 迭代器遍历所有值 |
| `popitem` | `(k)` | 返回并删除具有键 `k` 的项目 |
| `update` | `([e])` | 用 `e` 中的项目更新项目 |

## 集合

我们将考虑的最后一个数据结构是 `set` 对象。尽管集合论是数学和金融理论的基石，但对于 `set` 对象的实际应用并不太多。这些对象是其他对象的无序集合，每个元素只包含一次：

```py
In [131]: s = set(['u', 'd', 'ud', 'du', 'd', 'du'])
          s
Out[131]: {'d', 'du', 'u', 'ud'}

In [132]: t = set(['d', 'dd', 'uu', 'u'])
```

使用 `set` 对象，您可以像在数学集合论中一样实现操作。例如，您可以生成并集、交集和差异：

```py
In [133]: s.union(t)  # ①
Out[133]: {'d', 'dd', 'du', 'u', 'ud', 'uu'}

In [134]: s.intersection(t)  # ②
Out[134]: {'d', 'u'}

In [135]: s.difference(t)  # ③
Out[135]: {'du', 'ud'}

In [136]: t.difference(s)  # ④
Out[136]: {'dd', 'uu'}

In [137]: s.symmetric_difference(t)  # ⑤
Out[137]: {'dd', 'du', 'ud', 'uu'}
```

①

`s` 和 `t` 的全部。

②

在 `s` 和 `t` 中都有。

③

在 `s` 中但不在 `t` 中。

④

在 `t` 中但不在 `s` 中。

⑤

在其中一个但不是两者都。

`set`对象的一个应用是从`list`对象中消除重复项。例如：

```py
In [138]: from random import randint
          l = [randint(0, 10) for i in range(1000)]  # ①
          len(l)  # ②
Out[138]: 1000

In [139]: l[:20]
Out[139]: [10, 9, 2, 4, 5, 1, 7, 4, 6, 10, 9, 5, 4, 6, 10, 3, 4, 7, 0, 5]

In [140]: s = set(l)
          s
Out[140]: {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

①

1,000 个 0 到 10 之间的随机整数。

②

`l` 中的元素数量。

# 结论

基本的`Python`解释器已经提供了丰富灵活的数据结构。从金融的角度来看，以下可以被认为是最重要的：

基本数据类型

在金融中，`int`、`float`和`string`类提供了原子数据类型。

标准数据结构

`tuple`、`list`、`dict`和`set`类在金融领域有许多应用领域，其中`list`通常是最灵活的通用工作马。

# 进一步资源

本章重点讨论可能对金融算法和应用特别重要的问题。但是，它只能代表探索`Python`中数据结构和数据建模的起点。有许多宝贵的资源可供进一步深入了解。

书籍形式的良好参考资料包括：

+   Goodrich, Michael 等（2013）：*Python 数据结构与算法.* John Wiley & Sons, Hoboken, NJ.

+   Harrison, Matt (2017): *Python 3 图解指南*. Treading on Python Series.

+   Ramalho, Luciano (2016): *流畅的 Python*. O’Reilly, Beijing et al.

¹ [`Cython`库](http://www.cython.org)将静态类型和编译功能引入`Python`，与`C`中的相似。实际上，`Cython`是`Python`和`C`的混合语言。

² 在这里和后续讨论中，诸如*float*、*float 对象*等术语可互换使用，承认每个*float*也是一个*对象*。对于其他对象类型也是如此。

³ 参考[*http://en.wikipedia.org/wiki/Double-precision_floating-point_format*](http://en.wikipedia.org/wiki/Double-precision_floating-point_format)。

⁴ 在这里不可能详细介绍，但互联网上有大量关于正则表达式的信息，特别是针对`Python`。关于这个主题的介绍，请参阅 Fitzgerald, Michael (2012): *正则表达式入门*. O’Reilly, Sebastopol, CA.
