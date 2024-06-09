# 第六章：面向对象编程

> 软件工程的目的是控制复杂性，而不是创建它。
> 
> Pamela Zave

# 介绍

面向对象编程（OOP）是当今最流行的编程范式之一。正确使用时，它与过程式编程相比提供了许多优势。在许多情况下，OOP 似乎特别适用于金融建模和实施金融算法。然而，也有许多对 OOP 持批评态度的人，对 OOP 的单个方面甚至整个范式表示怀疑。本章对此持中立态度，认为 OOP 是一个重要的工具，可能不是每个问题的最佳解决方案，但应该是程序员和从事金融工作的量化人员的手头工具之一。

随着 OOP 的出现，一些新的术语也随之而来。本书和本章的最重要术语是（更多细节如下）：

类

对象类的抽象定义。例如，一个人类。

属性

类的特性（*类属性*）或类的实例（*实例属性*）的一个特征。例如，是哺乳动物或眼睛的颜色。

方法

可以在类上实现的操作。例如，行走。

参数

一个方法接受的输入参数以影响其行为。例如，三个步骤。

对象

类的一个实例。例如，有蓝眼睛的 Sandra。

实例化

创建基于抽象类的特定对象的过程。

转换为 Python 代码，实现人类示例的简单类可能如下所示。

```py
In [1]: class HumanBeing(object):  ![1](img/1.png)
            def __init__(self, first_name, eye_color):  ![2](img/2.png)
                self.first_name = first_name  ![3](img/3.png)
                self.eye_color = eye_color  ![4](img/4.png)
                self.position = 0  ![5](img/5.png)
            def walk_steps(self, steps):  ![6](img/6.png)
                self.position += steps  ![7](img/7.png)
```

![1](img/#co_object_orientated_programming_CO1-1)

类定义语句。

![2](img/#co_object_orientated_programming_CO1-2)

在实例化时调用的特殊方法。

![3](img/#co_object_orientated_programming_CO1-3)

名字属性初始化为参数值。

![4](img/#co_object_orientated_programming_CO1-4)

眼睛颜色属性初始化为参数值。

![5](img/#co_object_orientated_programming_CO1-5)

位置属性初始化为 0。

![6](img/#co_object_orientated_programming_CO1-6)

使用`steps`作为参数的步行方法定义。

![7](img/#co_object_orientated_programming_CO1-7)

给定`steps`值后改变位置的代码。

根据类定义，可以实例化并使用一个新的 Python 对象。

```py
In [2]: Sandra = HumanBeing('Sandra', 'blue')  ![1](img/1.png)

In [3]: Sandra.first_name  ![2](img/2.png)
Out[3]: 'Sandra'

In [4]: Sandra.position  ![2](img/2.png)
Out[4]: 0

In [5]: Sandra.walk_steps(5)  ![3](img/3.png)

In [6]: Sandra.position  ![4](img/4.png)
Out[6]: 5
```

![1](img/#co_object_orientated_programming_CO2-1)

实例化。

![2](img/#co_object_orientated_programming_CO2-2)

访问属性值。

![3](img/#co_object_orientated_programming_CO2-4)

调用方法。

![4](img/#co_object_orientated_programming_CO2-5)

访问更新后的`position`值。

有几个*人类方面*可能支持使用 OOP：

自然的思考方式

人类思维通常围绕着现实世界或抽象对象展开，比如汽车或金融工具。面向对象编程适合模拟具有其特征的这类对象。

降低复杂性

通过不同的方法，面向对象编程有助于降低问题或算法的复杂性，并逐个特征进行建模。

更好的用户界面

在许多情况下，面向对象编程可以实现更美观的用户界面和更紧凑的代码。例如，当查看`NumPy`的`ndarray`类或`pandas`的`DataFrame`类时，这一点变得显而易见。

Python 建模的方式

独立于面向对象编程的优缺点，它只是 Python 中的主导范式。这也是“在 Python 中一切皆为对象。”这句话的由来。面向对象编程还允许程序员构建自定义类，其实例的行为与标准 Python 类的任何其他实例相同。

也有一些*技术方面*可能支持面向对象编程：

抽象化

使用属性和方法可以构建对象的抽象、灵活的模型——重点放在相关的内容上，忽略不需要的内容。在金融领域，这可能意味着拥有一个以抽象方式模拟金融工具的通用类。这种类的实例将是由投资银行设计和提供的具体金融产品，例如。

模块化

面向对象编程简化了将代码拆分为多个模块的过程，然后将这些模块链接起来形成完整的代码基础。例如，可以通过一个类或两个类来建模股票上的欧式期权，一个用于基础股票，另一个用于期权本身。

继承

继承指的是一个类可以从另一个类继承属性和方法的概念。在金融领域，从一个通用的金融工具开始，下一个级别可能是一个通用的衍生金融工具，然后是一个欧式期权，再然后是一个欧式看涨期权。每个类都可以从更高级别的类中继承属性和方法。

聚合

聚合指的是一个对象至少部分由多个其他对象组成，这些对象可能是独立存在的。模拟欧式看涨期权的类可能具有其他对象的属性，例如基础股票和用于贴现的相关短期利率。表示股票和短期利率的对象也可以被其他对象独立使用。

组合

组合与聚合类似，但是这里的单个对象不能独立存在。考虑一个定制的固定利率互换合同和一个浮动利率互换合同。这两个腿不能独立于互换合同本身存在。

多态性

多态性可以呈现多种形式。在 Python 上下文中特别重要的是所谓的*鸭子类型*。这指的是可以在许多不同类及其实例上实现标准操作，而不需要准确知道正在处理的特定对象是什么。对于金融工具类，这可能意味着可以调用一个名为 `get_current_price()` 的方法，而不管对象的具体类型是什么（股票、期权、互换等）。

封装

此概念指的是仅通过公共方法访问类内部数据的方法。模拟股票的类可能有一个属性 `current_stock_price`。封装将通过方法 `get_current_stock_price()` 提供对属性值的访问，并将数据隐藏（使其私有化）。这种方法可能通过仅使用和可能更改属性值来避免意外效果。但是，对于如何使数据在 Python 类中私有化存在限制。

在更高层面上，软件工程中的*两个主要目标*可以总结如下：

可重用性

继承和多态等概念提高了代码的可重用性，增加了程序员的效率和生产力。它们还简化了代码的维护。

非冗余性

与此同时，这些方法允许构建几乎不冗余的代码，避免双重实现工作，减少调试和测试工作以及维护工作量。它还可能导致更小的总体代码基础。

本章按如下方式组织：

“Python 对象概览”

下一节将通过面向对象编程的视角简要介绍一些 Python 对象。

“Python 类基础”

本节介绍了 Python 中面向对象编程的核心要素，并以金融工具和投资组合头寸为主要示例。

“Python 数据模型”

本节讨论了 Python 数据模型的重要元素以及某些特殊方法所起的作用。

# Python 对象概览

本节通过面向对象编程程序员的眼光简要介绍了一些标准对象，这些对象在前一节中已经遇到过。

## int

为了简单起见，考虑一个整数对象。即使对于这样一个简单的 Python 对象，主要的面向对象编程（OOP）特征也是存在的。

```py
In [7]: n = 5  ![1](img/1.png)

In [8]: type(n)  ![2](img/2.png)
Out[8]: int

In [9]: n.numerator  ![3](img/3.png)
Out[9]: 5

In [10]: n.bit_length()  ![4](img/4.png)
Out[10]: 3

In [11]: n + n  ![5](img/5.png)
Out[11]: 10

In [12]: 2 * n  ![6](img/6.png)
Out[12]: 10

In [13]: n.__sizeof__()  ![7](img/7.png)
Out[13]: 28
```

![1](img/#co_object_orientated_programming_CO3-1)

新实例 `n`。

![2](img/#co_object_orientated_programming_CO3-2)

对象的类型。

![3](img/#co_object_orientated_programming_CO3-3)

一个属性。

![4](img/#co_object_orientated_programming_CO3-4)

一个方法。

![5](img/#co_object_orientated_programming_CO3-5)

应用 + 运算符（加法）。

![6](img/#co_object_orientated_programming_CO3-6)

应用 * 运算符（乘法）。

![7](img/#co_object_orientated_programming_CO3-7)

调用特殊方法`__sizeof__()`以获取内存使用情况（以字节为单位）。^(1)

## 列表

`list`对象有一些额外的方法，但基本上表现方式相同。

```py
In [14]: l = [1, 2, 3, 4]  ![1](img/1.png)

In [15]: type(l)  ![2](img/2.png)
Out[15]: list

In [16]: l[0]  ![3](img/3.png)
Out[16]: 1

In [17]: l.append(10)  ![4](img/4.png)

In [18]: l + l  ![5](img/5.png)
Out[18]: [1, 2, 3, 4, 10, 1, 2, 3, 4, 10]

In [19]: 2 * l  ![6](img/6.png)
Out[19]: [1, 2, 3, 4, 10, 1, 2, 3, 4, 10]

In [20]: sum(l)  ![7](img/7.png)
Out[20]: 20

In [21]: l.__sizeof__()  ![8](img/8.png)
Out[21]: 104
```

![1](img/#co_object_orientated_programming_CO4-1)

新实例`l`。

![2](img/#co_object_orientated_programming_CO4-2)

对象的类型。

![3](img/#co_object_orientated_programming_CO4-3)

通过索引选择元素。

![4](img/#co_object_orientated_programming_CO4-4)

一个方法。

![5](img/#co_object_orientated_programming_CO4-5)

应用+运算符（连接）。

![6](img/#co_object_orientated_programming_CO4-6)

应用*运算符（连接）。

![7](img/#co_object_orientated_programming_CO4-7)

应用标准 Python 函数`sum()`。

![8](img/#co_object_orientated_programming_CO4-8)

调用特殊方法`__sizeof__()`以获取内存使用情况（以字节为单位）。

## ndarray

`int`和`list`对象是标准的 Python 对象。`NumPy`的`ndarray`对象是一个来自开源包的“自定义”对象。

```py
In [22]: import numpy as np  ![1](img/1.png)

In [23]: a = np.arange(16).reshape((4, 4))  ![2](img/2.png)

In [24]: a  ![2](img/2.png)
Out[24]: array([[ 0,  1,  2,  3],
                [ 4,  5,  6,  7],
                [ 8,  9, 10, 11],
                [12, 13, 14, 15]])

In [25]: type(a)  ![3](img/3.png)
Out[25]: numpy.ndarray
```

![1](img/#co_object_orientated_programming_CO5-1)

导入`numpy`。

![2](img/#co_object_orientated_programming_CO5-2)

新实例`a`。

![3](img/#co_object_orientated_programming_CO5-4)

对象的类型。

尽管`ndarray`对象不是标准对象，但在许多情况下表现得就像是一个标准对象——这要归功于下文中解释的 Python 数据模型。

```py
In [26]: a.nbytes  ![1](img/1.png)
Out[26]: 128

In [27]: a.sum()  ![2](img/2.png)
Out[27]: 120

In [28]: a.cumsum(axis=0)  ![3](img/3.png)
Out[28]: array([[ 0,  1,  2,  3],
                [ 4,  6,  8, 10],
                [12, 15, 18, 21],
                [24, 28, 32, 36]])

In [29]: a + a  ![4](img/4.png)
Out[29]: array([[ 0,  2,  4,  6],
                [ 8, 10, 12, 14],
                [16, 18, 20, 22],
                [24, 26, 28, 30]])

In [30]: 2 * a  ![5](img/5.png)
Out[30]: array([[ 0,  2,  4,  6],
                [ 8, 10, 12, 14],
                [16, 18, 20, 22],
                [24, 26, 28, 30]])

In [31]: sum(a)  ![6](img/6.png)
Out[31]: array([24, 28, 32, 36])

In [32]: np.sum(a)  ![7](img/7.png)
Out[32]: 120

In [33]: a.__sizeof__()  ![8](img/8.png)
Out[33]: 112
```

![1](img/#co_object_orientated_programming_CO6-1)

一个属性。

![2](img/#co_object_orientated_programming_CO6-2)

一个方法（聚合）。

![3](img/#co_object_orientated_programming_CO6-3)

一个方法（没有聚合）。

![4](img/#co_object_orientated_programming_CO6-4)

应用+运算符（加法）。

![5](img/#co_object_orientated_programming_CO6-5)

应用*运算符（乘法）。

![6](img/#co_object_orientated_programming_CO6-6)

应用标准 Python 函数`sum()`。

![7](img/#co_object_orientated_programming_CO6-7)

应用`NumPy`通用函数`np.sum()`。

![8](img/#co_object_orientated_programming_CO6-8)

调用特殊方法`__sizeof__()`以获取内存使用情况（以字节为单位）。

## DataFrame

最后，快速查看`pandas`的`DataFrame`对象，因为其行为大多与`ndarray`对象相同。首先，基于`ndarray`对象实例化`DataFrame`对象。

```py
In [34]: import pandas as pd  ![1](img/1.png)

In [35]: df = pd.DataFrame(a, columns=list('abcd'))  ![2](img/2.png)

In [36]: type(df)  ![3](img/3.png)
Out[36]: pandas.core.frame.DataFrame
```

![1](img/#co_object_orientated_programming_CO7-1)

导入`pandas`。

![2](img/#co_object_orientated_programming_CO7-2)

新实例`df`。

![3](img/#co_object_orientated_programming_CO7-3)

对象的类型。

其次，查看属性、方法和操作。

```py
In [37]: df.columns  ![1](img/1.png)
Out[37]: Index(['a', 'b', 'c', 'd'], dtype='object')

In [38]: df.sum()  ![2](img/2.png)
Out[38]: a    24
         b    28
         c    32
         d    36
         dtype: int64

In [39]: df.cumsum()  ![3](img/3.png)
Out[39]:     a   b   c   d
         0   0   1   2   3
         1   4   6   8  10
         2  12  15  18  21
         3  24  28  32  36

In [40]: df + df  ![4](img/4.png)
Out[40]:     a   b   c   d
         0   0   2   4   6
         1   8  10  12  14
         2  16  18  20  22
         3  24  26  28  30

In [41]: 2 * df  ![5](img/5.png)
Out[41]:     a   b   c   d
         0   0   2   4   6
         1   8  10  12  14
         2  16  18  20  22
         3  24  26  28  30

In [42]: np.sum(df)  ![6](img/6.png)
Out[42]: a    24
         b    28
         c    32
         d    36
         dtype: int64

In [43]: df.__sizeof__()  ![7](img/7.png)
Out[43]: 208
```

![1](img/#co_object_orientated_programming_CO8-1)

一个属性。

![2](img/#co_object_orientated_programming_CO8-2)

一个方法（聚合）。

![3](img/#co_object_orientated_programming_CO8-3)

一个方法（无聚合）。

![4](img/#co_object_orientated_programming_CO8-4)

应用+运算符（加法）。

![5](img/#co_object_orientated_programming_CO8-5)

应用*运算符（乘法）。

![6](img/#co_object_orientated_programming_CO8-6)

应用`NumPy`通用函数`np.sum()`。

![7](img/#co_object_orientated_programming_CO8-7)

调用特殊方法`__sizeof__()`以获取以字节为单位的内存使用情况。

# Python 类的基础知识

本节涉及主要概念和具体语法，以利用 Python 中的 OOP。当前的背景是构建自定义类来模拟无法轻松、高效或适当地由现有 Python 对象类型建模的对象类型。在*金融工具*的示例中，只需两行代码即可创建一个新的 Python 类。

```py
In [44]: class FinancialInstrument(object):  ![1](img/1.png)
             pass  ![2](img/2.png)

In [45]: fi = FinancialInstrument()  ![3](img/3.png)

In [46]: type(fi)  ![4](img/4.png)
Out[46]: __main__.FinancialInstrument

In [47]: fi  ![4](img/4.png)
Out[47]: <__main__.FinancialInstrument at 0x10a21c828>

In [48]: fi.__str__()  ![5](img/5.png)
Out[48]: '<__main__.FinancialInstrument object at 0x10a21c828>'

In [49]: fi.price = 100  ![6](img/6.png)

In [50]: fi.price  ![6](img/6.png)
Out[50]: 100
```

![1](img/#co_object_orientated_programming_CO9-1)

类定义语句。^(2)

![2](img/#co_object_orientated_programming_CO9-2)

一些代码；这里只是`pass`关键字。

![3](img/#co_object_orientated_programming_CO9-3)

一个名为`fi`的类的新实例。

![4](img/#co_object_orientated_programming_CO9-4)

每个 Python 对象都带有某些特殊属性和方法（来自`object`）；这里调用了用于检索字符串表示的特殊方法。

![5](img/#co_object_orientated_programming_CO9-6)

所谓的数据属性 —— 与常规属性相对 —— 可以为每个对象即时定义。

![6](img/#co_object_orientated_programming_CO9-7)

一个重要的特殊方法是`__init__`，它在每次实例化对象时被调用。它以对象自身（按照惯例为`self`）和可能的多个其他参数作为参数。除了实例属性之外

```py
In [51]: class FinancialInstrument(object):
             author = 'Yves Hilpisch'  ![1](img/1.png)
             def __init__(self, symbol, price):  ![2](img/2.png)
                 self.symbol = symbol  ![3](img/3.png)
                 self.price = price  ![3](img/3.png)

In [52]: FinancialInstrument.author  ![1](img/1.png)
Out[52]: 'Yves Hilpisch'

In [53]: aapl = FinancialInstrument('AAPL', 100)  ![4](img/4.png)

In [54]: aapl.symbol  ![5](img/5.png)
Out[54]: 'AAPL'

In [55]: aapl.author  ![6](img/6.png)
Out[55]: 'Yves Hilpisch'

In [56]: aapl.price = 105  ![7](img/7.png)

In [57]: aapl.price  ![7](img/7.png)
Out[57]: 105
```

![1](img/#co_object_orientated_programming_CO10-1)

类属性的定义（=每个实例都继承的）。

![2](img/#co_object_orientated_programming_CO10-2)

在初始化期间调用特殊方法`__init__`。

![3](img/#co_object_orientated_programming_CO10-3)

实例属性的定义（=每个实例都是个别的）。

![4](img/#co_object_orientated_programming_CO10-6)

一个名为`fi`的类的新实例。

![5](img/#co_object_orientated_programming_CO10-7)

访问实例属性。

![6](img/#co_object_orientated_programming_CO10-8)

访问类属性。

![7](img/#co_object_orientated_programming_CO10-9)

更改实例属性的值。

金融工具的价格经常变动，金融工具的符号可能不会变动。为了向类定义引入封装，可以定义两个方法`get_price()`和`set_price()`。接下来的代码还额外继承了之前的类定义（不再继承自`object`）。

```py
In [58]: class FinancialInstrument(FinancialInstrument):  ![1](img/1.png)
             def get_price(self):  ![2](img/2.png)
                 return self.price  ![2](img/2.png)
             def set_price(self, price):  ![3](img/3.png)
                 self.price = price  ![4](img/4.png)

In [59]: fi = FinancialInstrument('AAPL', 100)  ![5](img/5.png)

In [60]: fi.get_price()  ![6](img/6.png)
Out[60]: 100

In [61]: fi.set_price(105)  ![7](img/7.png)

In [62]: fi.get_price()  ![6](img/6.png)
Out[62]: 105

In [63]: fi.price  ![8](img/8.png)
Out[63]: 105
```

![1](img/#co_object_orientated_programming_CO11-1)

通过从上一个版本继承的方式进行类定义。

![2](img/#co_object_orientated_programming_CO11-2)

定义`get_price`方法。

![3](img/#co_object_orientated_programming_CO11-4)

定义`set_price`方法……

![4](img/#co_object_orientated_programming_CO11-5)

……并根据参数值更新实例属性值。

![5](img/#co_object_orientated_programming_CO11-6)

基于新的类定义创建一个名为`fi`的新实例。

![6](img/#co_object_orientated_programming_CO11-7)

调用`get_price()`方法来读取实例属性值。

![7](img/#co_object_orientated_programming_CO11-8)

通过`set_price()`更新实例属性值。

![8](img/#co_object_orientated_programming_CO11-10)

直接访问实例属性。

封装通常的目标是隐藏用户对类的操作中的数据。添加相应的方法，有时称为*getter*和*setter*方法，是实现此目标的一部分。然而，这并不阻止用户直接访问和操作实例属性。这就是*私有*实例属性发挥作用的地方。它们由两个前导下划线定义。

```py
In [64]: class FinancialInstrument(object):
             def __init__(self, symbol, price):
                 self.symbol = symbol
                 self.__price = price  ![1](img/1.png)
             def get_price(self):
                 return self.__price
             def set_price(self, price):
                 self.__price = price

In [65]: fi = FinancialInstrument('AAPL', 100)

In [66]: fi.get_price()  ![2](img/2.png)
Out[66]: 100

In [67]: fi.__price  ![3](img/3.png)

         ----------------------------------------
         AttributeErrorTraceback (most recent call last)
         <ipython-input-67-74c0dc05c9ae> in <module>()
----> 1 fi.__price  ![3](img/3.png)

         AttributeError: 'FinancialInstrument' object has no attribute '__price'

In [68]: fi._FinancialInstrument__price  ![4](img/4.png)
Out[68]: 100

In [69]: fi._FinancialInstrument__price = 105  ![4](img/4.png)

In [70]: fi.set_price(100)  ![5](img/5.png)
```

![1](img/#co_object_orientated_programming_CO12-1)

价格被定义为私有实例属性。

![2](img/#co_object_orientated_programming_CO12-2)

方法`get_price()`返回其值。

![3](img/#co_object_orientated_programming_CO12-3)

尝试直接访问属性会引发错误。

![4](img/#co_object_orientated_programming_CO12-5)

通过在类名前添加单个下划线，仍然可以直接访问和操作。

![5](img/#co_object_orientated_programming_CO12-7)

将价格恢复到其原始值。

###### 注意

尽管封装基本上可以通过私有实例属性和处理它们的方法来实现 Python 类，但无法完全强制隐藏数据不让用户访问。从这个意义上说，这更像是 Python 中的一种工程原则，而不是 Python 类的技术特性。

考虑另一个模拟金融工具投资组合头寸的类。通过两个类，*聚合*的概念很容易说明。`PortfolioPosition`类的一个实例将`FinancialInstrument`类的一个实例作为属性值。添加一个实例属性，比如`position_size`，然后可以计算出例如头寸价值。

```py
In [71]: class PortfolioPosition(object):
             def __init__(self, financial_instrument, position_size):
                 self.position = financial_instrument  ![1](img/1.png)
                 self.__position_size = position_size  ![2](img/2.png)
             def get_position_size(self):
                 return self.__position_size
             def update_position_size(self, position_size):
                 self.__position_size = position_size
             def get_position_value(self):
                 return self.__position_size * \
                        self.position.get_price()  ![3](img/3.png)

In [72]: pp = PortfolioPosition(fi, 10)

In [73]: pp.get_position_size()
Out[73]: 10

In [74]: pp.get_position_value()  ![3](img/3.png)
Out[74]: 1000

In [75]: pp.position.get_price()  ![4](img/4.png)
Out[75]: 100

In [76]: pp.position.set_price(105)  ![5](img/5.png)

In [77]: pp.get_position_value()  ![6](img/6.png)
Out[77]: 1050
```

![1](img/#co_object_orientated_programming_CO13-1)

基于`FinancialInstrument`类的实例的实例属性。

![2](img/#co_object_orientated_programming_CO13-2)

`PortfolioPosition`类的私有实例属性。

![3](img/#co_object_orientated_programming_CO13-3)

根据属性计算位置值。

![4](img/#co_object_orientated_programming_CO13-5)

附加到实例属性对象的方法可以直接访问（也可能被隐藏）。

![5](img/#co_object_orientated_programming_CO13-6)

更新金融工具的价格。

![6](img/#co_object_orientated_programming_CO13-7)

根据更新后的价格计算新位置值。

# Python 数据模型

前一节的示例已经突出了所谓的 Python 数据或对象模型的一些方面（参见[*https://docs.python.org/3/reference/datamodel.html*](https://docs.python.org/3/reference/datamodel.html)）。Python 数据模型允许设计与 Python 基本语言构造一致交互的类。除其他外，它支持（参见 Ramalho（2015），第 4 页）以下任务和结构：

+   迭代

+   集合处理

+   属性访问

+   运算符重载

+   函数和方法调用

+   对象创建和销毁

+   字符串表示（例如，用于打印）

+   受管理的上下文（即`with`块）。

由于 Python 数据模型非常重要，本节专门介绍了一个示例，探讨了其中的几个方面。示例可在 Ramalho（2015）的书中找到，并进行了微调。它实现了一个一维，三元素向量的类（想象一下欧几里德空间中的向量）。首先，特殊方法`__init__`：

```py
In [78]: class Vector(object):
             def __init__(self, x=0, y=0, z=0):  ![1](img/1.png)
                 self.x = x  ![1](img/1.png)
                 self.y = y  ![1](img/1.png)
                 self.z = z  ![1](img/1.png)

In [79]: v = Vector(1, 2, 3)  ![2](img/2.png)

In [80]: v  ![3](img/3.png)
Out[80]: <__main__.Vector at 0x10a245d68>
```

![1](img/#co_object_orientated_programming_CO14-1)

三个预初始化的实例属性（想象成三维空间）。

![2](img/#co_object_orientated_programming_CO14-5)

名为`v`的类的新实例。

![3](img/#co_object_orientated_programming_CO14-6)

默认字符串表示。

特殊方法`__str__`允许定义自定义字符串表示。

```py
In [81]: class Vector(Vector):
             def __repr__(self):
                 return 'Vector(%r, %r, %r)' % (self.x, self.y, self.z)

In [82]: v = Vector(1, 2, 3)

In [83]: v  ![1](img/1.png)
Out[83]: Vector(1, 2, 3)

In [84]: print(v)  ![1](img/1.png)

         Vector(1, 2, 3)
```

![1](img/#co_object_orientated_programming_CO15-1)

新的字符串表示。

`abs()`和`bool()`是两个标准的 Python 函数，它们在`Vector`类上的行为可以通过特殊方法`__abs__`和`__bool__`来定义。

```py
In [85]: class Vector(Vector):
             def __abs__(self):
                 return (self.x ** 2 +  self.y ** 2 +
                         self.z ** 2) ** 0.5  ![1](img/1.png)

             def __bool__(self):
                 return bool(abs(self))

In [86]: v = Vector(1, 2, -1)  ![2](img/2.png)

In [87]: abs(v)
Out[87]: 2.449489742783178

In [88]: bool(v)
Out[88]: True

In [89]: v = Vector()  ![3](img/3.png)

In [90]: v  ![3](img/3.png)
Out[90]: Vector(0, 0, 0)

In [91]: abs(v)
Out[91]: 0.0

In [92]: bool(v)
Out[92]: False
```

![1](img/#co_object_orientated_programming_CO16-1)

返回给定三个属性值的欧几里德范数。

![2](img/#co_object_orientated_programming_CO16-2)

具有非零属性值的新`Vector`对象。

![3](img/#co_object_orientated_programming_CO16-3)

仅具有零属性值的新`Vector`对象。

如多次显示的那样，`+`和`*`运算符几乎可以应用于任何 Python 对象。其行为是通过特殊方法`__add__`和`__mul__`定义的。

```py
In [93]: class Vector(Vector):
             def __add__(self, other):
                 x = self.x + other.x
                 y = self.y + other.y
                 z = self.z + other.z
                 return Vector(x, y, z)  ![1](img/1.png)

             def __mul__(self, scalar):
                 return Vector(self.x * scalar,
                               self.y * scalar,
                               self.z * scalar)  ![1](img/1.png)

In [94]: v = Vector(1, 2, 3)

In [95]: v + Vector(2, 3, 4)
Out[95]: Vector(3, 5, 7)

In [96]: v * 2
Out[96]: Vector(2, 4, 6)
```

![1](img/#co_object_orientated_programming_CO17-1)

在这种情况下，两个特殊方法都返回自己的类型对象。

另一个标准的 Python 函数是 `len()`，它给出对象的长度，即元素的数量。当在对象上调用时，此函数访问特殊方法 `__len__`。另一方面，特殊方法 `__getitem__` 使通过方括号表示法进行索引成为可能。

```py
In [97]: class Vector(Vector):
             def __len__(self):
                 return 3  ![1](img/1.png)

             def __getitem__(self, i):
                 if i in [0, -3]: return self.x
                 elif i in [1, -2]: return self.y
                 elif i in [2, -1]: return self.z
                 else: raise IndexError('Index out of range.')

In [98]: v = Vector(1, 2, 3)

In [99]: len(v)
Out[99]: 3

In [100]: v[0]
Out[100]: 1

In [101]: v[-2]
Out[101]: 2

In [102]: v[3]

          ----------------------------------------
          IndexErrorTraceback (most recent call last)
          <ipython-input-102-0f5531c4b93d> in <module>()
----> 1 v[3]

          <ipython-input-97-eef2cdc22510> in __getitem__(self, i)
      7         elif i in [1, -2]: return self.y
      8         elif i in [2, -1]: return self.z
----> 9         else: raise IndexError('Index out of range.')

          IndexError: Index out of range.
```

![1](img/#co_object_orientated_programming_CO18-1)

`Vector` 类的所有实例都有长度为三。

最后，特殊方法 `__iter__` 定义了对对象元素进行迭代的行为。定义了此操作的对象称为*可迭代的*。例如，所有集合和容器都是可迭代的。

```py
In [103]: class Vector(Vector):
              def __iter__(self):
                  for i in range(len(self)):
                      yield self[i]

In [104]: v = Vector(1, 2, 3)

In [105]: for i in range(3):  ![1](img/1.png)
              print(v[i])  ![1](img/1.png)

          1
          2
          3

In [106]: for coordinate in v:  ![2](img/2.png)
              print(coordinate)  ![2](img/2.png)

          1
          2
          3
```

![1](img/#co_object_orientated_programming_CO19-1)

使用索引值进行间接迭代（通过`__getitem__`）。

![2](img/#co_object_orientated_programming_CO19-3)

对类实例进行直接迭代（使用`__iter__`）。

###### 提示

Python 数据模型允许定义与标准 Python 操作符、函数等无缝交互的 Python 类。这使得 Python 成为一种相当灵活的编程语言，可以轻松地通过新类和对象类型进行增强。

总之，子节 “向量类” 在单个代码块中提供了 `Vector` 类的定义。

# 结论

本章从理论上和基于 Python 示例介绍了面向对象编程（OOP）的概念和方法。OOP 是 Python 中使用的主要编程范式之一。它不仅允许建模和实现相当复杂的应用程序，还允许创建自定义对象，这些对象由于灵活的 Python 数据模型，与标准 Python 对象表现得相似。尽管有许多批评者反对 OOP，但可以肯定地说，当达到一定复杂程度时，它为 Python 程序员和量化人员提供了强大的手段和工具。在 [Link to Come] 中讨论和展示的衍生定价包呈现了这样一个情况，其中 OOP 似乎是唯一合理的编程范式，以处理固有的复杂性和对抽象的需求。

# 更多资源

关于面向对象编程以及特别是 Python 编程和 Python OOP 的一般和宝贵的在线资源：

+   [面向对象编程讲义](https://atomicobject.com/resources/oo-programming/introduction-motivation-for-oo)

+   [Python 中的面向对象编程](http://python-textbok.readthedocs.io/en/1.0/)

有关 Python 面向对象编程（OOP）和 Python 数据模型的书籍资源：

+   Ramalho, Luciano（2016）：*流畅的 Python*。 O’Reilly，北京等。

# Python 代码

## 向量类

```py
In [107]: class Vector(object):
              def __init__(self, x=0, y=0, z=0):
                  self.x = x
                  self.y = y
                  self.z = z

              def __repr__(self):
                  return 'Vector(%r, %r, %r)' % (self.x, self.y, self.z)

              def __abs__(self):
                  return (self.x ** 2 +  self.y ** 2 + self.z ** 2) ** 0.5

              def __bool__(self):
                  return bool(abs(self))

              def __add__(self, other):
                  x = self.x + other.x
                  y = self.y + other.y
                  z = self.z + other.z
                  return Vector(x, y, z)

              def __mul__(self, scalar):
                  return Vector(self.x * scalar,
                                self.y * scalar,
                                self.z * scalar)

              def __len__(self):
                  return 3

              def __getitem__(self, i):
                  if i in [0, -3]: return self.x
                  elif i in [1, -2]: return self.y
                  elif i in [2, -1]: return self.z
                  else: raise IndexError('Index out of range.')

              def __iter__(self):
                  for i in range(len(self)):
                      yield self[i]
```

^(1) Python 中的特殊属性和方法以双下划线开头和结尾，例如 `__XYZ__`。

^(2) 类名采用驼峰命名法是推荐的方式。然而，如果没有歧义，也可以采用小写命名，比如`financial_instrument`。
