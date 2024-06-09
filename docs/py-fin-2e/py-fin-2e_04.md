# 第四章：使用 NumPy 进行数值计算

> 计算机是无用的。它们只能给出答案。
> 
> 巴勃罗·毕加索

# 介绍

本章介绍了 `Python` 的基本数据类型和数据结构。尽管 `Python` 解释器本身已经带来了丰富的数据结构，但 `NumPy` 和其他库以有价值的方式添加了这些数据结构。

本章组织如下：

数据数组

本节详细讨论了数组的概念，并说明了在 Python 中处理数据数组的基本选项。

NumPy 数据结构

本节致力于介绍 `NumPy` `ndarray` 类的特性和功能，并展示了该类对科学和金融应用的一些好处。

代码向量化

本节说明了，由于 `NumPy` 的数组类，向量化代码很容易实现，从而导致代码更紧凑，性能更好。

本章涵盖了以下数据结构：

| 对象类型 | 含义 | 用法/模型 |
| --- | --- | --- |
| `ndarray`（常规） | n 维数组对象 | 大量数值数据的大数组 |
| `ndarray`（记录） | 二维数组对象 | 以列组织的表格数据 |

本章组织如下：

[“数据数组”](#arrays)

本节讨论了使用纯 Python 代码处理数据数组的方法。

[待添加链接]

这是关于常规 `NumPy` `ndarray` 类的核心部分；它是几乎所有数据密集型 Python 使用案例中的主要工具。

[待添加链接]

这个简短的部分介绍了用于处理带有列的表格数据的结构化（或记录）`ndarray` 对象。

[“代码的向量化”](#vec_code)

在本节中，讨论了代码的向量化及其好处；该部分还讨论了在某些情况下内存布局的重要性。

# 数据数组

前一章表明 `Python` 提供了一些非常有用和灵活的通用数据结构。特别是，`list` 对象可以被认为是一个真正的工作马，具有许多方便的特性和应用领域。在一般情况下，使用这样一个灵活的（可变的）数据结构的代价在于相对较高的内存使用量，较慢的性能或两者兼有。然而，科学和金融应用通常需要对特殊数据结构进行高性能操作。在这方面最重要的数据结构之一是*数组*。数组通常以行和列的形式结构化其他（基本）相同数据类型的对象。

暂时假设我们仅使用数字，尽管这个概念也可以推广到其他类型的数据。在最简单的情况下，一维数组在数学上表示为*向量*，通常由`float`对象内部表示为实数的一行或一列元素组成。在更普遍的情况下，数组表示为*i* × *j* *矩阵*的元素。这个概念在三维中也可以推广为*i* × *j* × *k* *立方体*的元素以及形状为*i* × *j* × *k* × *l* × …的一般*n*维数组。

线性代数和向量空间理论等数学学科说明了这些数学结构在许多科学学科和领域中的重要性。因此，设计一个专门的数据结构类来方便和高效地处理数组可能是非常有益的。这就是`Python`库`NumPy`的作用所在，其`ndarray`类应运而生。在下一节介绍其强大的`ndarray`类之前，本节展示了两种处理数组的替代方法。

## 使用Python列表的数组

在转向`NumPy`之前，让我们首先用上一节介绍的内置数据结构构建数组。`list`对象特别适用于完成这项任务。一个简单的`list`已经可以被视为一维数组：

```py
In [1]: v = [0.5, 0.75, 1.0, 1.5, 2.0]  ①
```

[①](#co_numerical_computing_with_numpy_CO1-1)

`list`对象与数字。

由于`list`对象可以包含任意其他对象，它们也可以包含其他`list`对象。通过嵌套`list`对象，可以轻松构建二维和更高维的数组：

```py
In [2]: m = [v, v, v]  ①
        m  ②
Out[2]: [[0.5, 0.75, 1.0, 1.5, 2.0],
         [0.5, 0.75, 1.0, 1.5, 2.0],
         [0.5, 0.75, 1.0, 1.5, 2.0]]
```

[①](#co_numerical_computing_with_numpy_CO2-1)

`list`对象与`list`对象…

[②](#co_numerical_computing_with_numpy_CO2-2)

… 得到一个数字矩阵。

我们还可以通过简单的索引选择行或通过双重索引选择单个元素（然而，选择整列并不那么容易）：

```py
In [3]: m[1]
Out[3]: [0.5, 0.75, 1.0, 1.5, 2.0]

In [4]: m[1][0]
Out[4]: 0.5
```

嵌套可以进一步推广到更一般的结构：

```py
In [5]: v1 = [0.5, 1.5]
        v2 = [1, 2]
        m = [v1, v2]
        c = [m, m]  ①
        c
Out[5]: [[[0.5, 1.5], [1, 2]], [[0.5, 1.5], [1, 2]]]

In [6]: c[1][1][0]
Out[6]: 1
```

[①](#co_numerical_computing_with_numpy_CO3-1)

立方数。

请注意，刚刚介绍的对象组合方式通常使用对原始对象的引用指针。这在实践中意味着什么？让我们看看以下操作：

```py
In [7]: v = [0.5, 0.75, 1.0, 1.5, 2.0]
        m = [v, v, v]
        m
Out[7]: [[0.5, 0.75, 1.0, 1.5, 2.0],
         [0.5, 0.75, 1.0, 1.5, 2.0],
         [0.5, 0.75, 1.0, 1.5, 2.0]]
```

现在修改`v`对象的第一个元素的值，看看`m`对象会发生什么变化：

```py
In [8]: v[0] = 'Python'
        m
Out[8]: [['Python', 0.75, 1.0, 1.5, 2.0],
         ['Python', 0.75, 1.0, 1.5, 2.0],
         ['Python', 0.75, 1.0, 1.5, 2.0]]
```

通过使用`copy`模块的`deepcopy`函数，可以避免这种情况：

```py
In [9]: from copy import deepcopy
        v = [0.5, 0.75, 1.0, 1.5, 2.0]
        m = 3 * [deepcopy(v), ]  ①
        m
Out[9]: [[0.5, 0.75, 1.0, 1.5, 2.0],
         [0.5, 0.75, 1.0, 1.5, 2.0],
         [0.5, 0.75, 1.0, 1.5, 2.0]]

In [10]: v[0] = 'Python'  ②
         m  ③
Out[10]: [[0.5, 0.75, 1.0, 1.5, 2.0],
          [0.5, 0.75, 1.0, 1.5, 2.0],
          [0.5, 0.75, 1.0, 1.5, 2.0]]
```

[①](#co_numerical_computing_with_numpy_CO4-1)

使用物理副本而不是引用指针。

[②](#co_numerical_computing_with_numpy_CO4-2)

因此，对原始对象的更改…

[③](#co_numerical_computing_with_numpy_CO4-3)

… 不再有任何影响。

## Python数组类

Python中有一个专用的`array`模块可用。正如您可以在文档页面上阅读到的（参见[*https://docs.python.org/3/library/array.html*](https://docs.python.org/3/library/array.html)）：

> 该模块定义了一种对象类型，可以紧凑地表示基本值的数组：字符、整数、浮点数。数组是序列类型，并且行为非常像列表，只是存储在其中的对象类型受到限制。类型在对象创建时通过使用类型代码（一个单个字符）来指定。

考虑以下代码，将一个`list`对象实例化为一个`array`对象。

```py
In [11]: v = [0.5, 0.75, 1.0, 1.5, 2.0]

In [12]: import array

In [13]: a = array.array('f', v)  ①
         a
Out[13]: array('f', [0.5, 0.75, 1.0, 1.5, 2.0])

In [14]: a.append(0.5)  ②
         a
Out[14]: array('f', [0.5, 0.75, 1.0, 1.5, 2.0, 0.5])

In [15]: a.extend([5.0, 6.75])  ②
         a
Out[15]: array('f', [0.5, 0.75, 1.0, 1.5, 2.0, 0.5, 5.0, 6.75])

In [16]: 2 * a  ③
Out[16]: array('f', [0.5, 0.75, 1.0, 1.5, 2.0, 0.5, 5.0, 6.75, 0.5, 0.75, 1.0, 1.5, 2.0, 0.5, 5.0, 6.75])
```

[①](#co_numerical_computing_with_numpy_CO5-1)

使用`float`作为类型代码实例化`array`对象。

[②](#co_numerical_computing_with_numpy_CO5-2)

主要方法的工作方式类似于`list`对象的方法。

[③](#co_numerical_computing_with_numpy_CO5-4)

虽然“标量乘法”原理上可行，但结果不是数学上预期的；而是元素被重复。

尝试附加与指定数据类型不同的对象会引发`TypeError`。

```py
In [17]: # a.append('string') ①

In [18]: a.tolist()  ②
Out[18]: [0.5, 0.75, 1.0, 1.5, 2.0, 0.5, 5.0, 6.75]
```

[①](#co_numerical_computing_with_numpy_CO6-1)

仅能附加`float`对象；其他数据类型/类型代码会引发错误。

[②](#co_numerical_computing_with_numpy_CO6-2)

然而，如果需要这样的灵活性，`array`对象可以轻松转换回`list`对象。

`array`类的一个优点是它具有内置的存储和检索功能。

```py
In [19]: f = open('array.apy', 'wb')  ①
         a.tofile(f)  ②
         f.close()  ③

In [20]: with open('array.apy', 'wb') as f:  ![4](images/4.png)
             a.tofile(f)  ![4](images/4.png)

In [21]: !ls -n arr*  ![5](images/5.png)

         -rw-r--r--@ 1 503  20  32 29 Dez 17:08 array.apy
```

[①](#co_numerical_computing_with_numpy_CO7-1)

打开一个用于写入二进制数据的磁盘上的文件。

[②](#co_numerical_computing_with_numpy_CO7-2)

将`array`数据写入文件。

[③](#co_numerical_computing_with_numpy_CO7-3)

关闭文件。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO7-4)

或者，可以使用`with`上下文执行相同的操作。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO7-6)

这显示了磁盘上写入的文件。

与以前一样，从磁盘读取数据时，`array`对象的数据类型很重要。

```py
In [22]: b = array.array('f')  ①

In [23]: with open('array.apy', 'rb') as f:  ②
             b.fromfile(f, 5)  ③

In [24]: b  ![4](images/4.png)
Out[24]: array('f', [0.5, 0.75, 1.0, 1.5, 2.0])

In [25]: b = array.array('d')  ![5](images/5.png)

In [26]: with open('array.apy', 'rb') as f:
             b.fromfile(f, 2)  ![6](images/6.png)

In [27]: b  ![7](images/7.png)
Out[27]: array('d', [0.0004882813645963324, 0.12500002956949174])
```

[①](#co_numerical_computing_with_numpy_CO8-1)

使用类型代码`float`创建一个新的`array`对象。

[②](#co_numerical_computing_with_numpy_CO8-2)

打开文件以读取二进制数据...

[③](#co_numerical_computing_with_numpy_CO8-3)

...并在`b`对象中读取五个元素。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO8-4)

使用类型代码`double`创建一个新的`array`对象。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO8-5)

从文件中读取两个元素。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO8-6)

类型代码的差异导致“错误”的数字。

[![7](images/7.png)](#co_numerical_computing_with_numpy_CO8-7)

# 常规NumPy数组

显然，使用`list`对象构成数组结构有些作用。但这并不是真正方便的方式，而且`list`类并没有为此特定目标而构建。它的范围更广泛，更一般。`array`类已经稍微更专业一些，提供了一些有用的特性来处理数据数组。然而，某种“高度”专业化的类因此可能真的对处理数组类型的结构非常有益。

## 基础知识

这样一个专门的类就是`numpy.ndarray`类，它的特定目标是方便且高效地处理*n*维数组，即以高性能的方式。这个类的基本处理最好通过示例来说明：

```py
In [28]: import numpy as np  ①

In [29]: a = np.array([0, 0.5, 1.0, 1.5, 2.0])  ②
         a
Out[29]: array([ 0. ,  0.5,  1. ,  1.5,  2. ])

In [30]: type(a)  ②
Out[30]: numpy.ndarray

In [31]: a = np.array(['a', 'b', 'c'])  ③
         a
Out[31]: array(['a', 'b', 'c'],
               dtype='<U1')

In [32]: a = np.arange(2, 20, 2)  ![4](images/4.png)
         a
Out[32]: array([ 2,  4,  6,  8, 10, 12, 14, 16, 18])

In [33]: a = np.arange(8, dtype=np.float)  ![5](images/5.png)
         a
Out[33]: array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.])

In [34]: a[5:]  ![6](images/6.png)
Out[34]: array([ 5.,  6.,  7.])

In [35]: a[:2]  ![6](images/6.png)
Out[35]: array([ 0.,  1.])
```

[①](#co_numerical_computing_with_numpy_CO9-1)

导入`numpy`包。

[②](#co_numerical_computing_with_numpy_CO9-2)

通过`list`对象中的浮点数创建一个`ndarray`对象。

[③](#co_numerical_computing_with_numpy_CO9-4)

通过`list`对象中的字符串创建一个`ndarray`对象。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO9-5)

`np.arange`的工作方式类似于`range`。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO9-6)

然而，它接受附加输入`dtype`参数。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO9-7)

对于一维的`ndarray`对象，索引的工作方式与平常一样。

`ndarray`类的一个重要特性是*内置方法的多样性*。例如：

```py
In [36]: a.sum()  ①
Out[36]: 28.0

In [37]: a.std()  ②
Out[37]: 2.2912878474779199

In [38]: a.cumsum()  ③
Out[38]: array([  0.,   1.,   3.,   6.,  10.,  15.,  21.,  28.])
```

[①](#co_numerical_computing_with_numpy_CO10-1)

所有元素的总和。

[②](#co_numerical_computing_with_numpy_CO10-2)

元素的标准偏差。

[③](#co_numerical_computing_with_numpy_CO10-3)

所有元素的累积和（从索引位置0开始）。

另一个重要特性是对`ndarray`对象定义的（向量化的）*数学运算*：

```py
In [39]: l = [0., 0.5, 1.5, 3., 5.]
         2 * l  ①
Out[39]: [0.0, 0.5, 1.5, 3.0, 5.0, 0.0, 0.5, 1.5, 3.0, 5.0]

In [40]: a
Out[40]: array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.])

In [41]: 2 * a  ②
Out[41]: array([  0.,   2.,   4.,   6.,   8.,  10.,  12.,  14.])

In [42]: a ** 2  ③
Out[42]: array([  0.,   1.,   4.,   9.,  16.,  25.,  36.,  49.])

In [43]: 2 ** a  ![4](images/4.png)
Out[43]: array([   1.,    2.,    4.,    8.,   16.,   32.,   64.,  128.])

In [44]: a ** a  ![5](images/5.png)
Out[44]: array([  1.00000000e+00,   1.00000000e+00,   4.00000000e+00,
                  2.70000000e+01,   2.56000000e+02,   3.12500000e+03,
                  4.66560000e+04,   8.23543000e+05])
```

[①](#co_numerical_computing_with_numpy_CO11-1)

与`list`对象的“标量乘法”导致元素的重复。

[②](#co_numerical_computing_with_numpy_CO11-2)

相比之下，使用`ndarray`对象实现了适当的标量乘法，例如。

[③](#co_numerical_computing_with_numpy_CO11-3)

这个计算每个元素的平方值。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO11-4)

这解释了`ndarray`的元素作为幂。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO11-5)

这个计算每个元素的自身的幂。

`NumPy`包的另一个重要功能是*通用函数*。它们在一般情况下对`ndarray`对象以及基本Python数据类型进行操作。然而，当将通用函数应用于Python `float`对象时，需要注意与`math`模块中相同功能的性能降低。

```py
In [45]: np.exp(a)  ①
Out[45]: array([  1.00000000e+00,   2.71828183e+00,   7.38905610e+00,
                  2.00855369e+01,   5.45981500e+01,   1.48413159e+02,
                  4.03428793e+02,   1.09663316e+03])

In [46]: np.sqrt(a)  ②
Out[46]: array([ 0.        ,  1.        ,  1.41421356,  1.73205081,  2.        ,
                 2.23606798,  2.44948974,  2.64575131])

In [47]: np.sqrt(2.5)  ③
Out[47]: 1.5811388300841898

In [48]: import math  ![4](images/4.png)

In [49]: math.sqrt(2.5)  ![4](images/4.png)
Out[49]: 1.5811388300841898

In [50]: # math.sqrt(a) ![5](images/5.png)

In [51]: %timeit np.sqrt(2.5)  ![6](images/6.png)

         703 ns ± 17.9 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)

In [52]: %timeit math.sqrt(2.5)  ![7](images/7.png)

         107 ns ± 1.48 ns per loop (mean ± std. dev. of 7 runs, 10000000 loops each)
```

[①](#co_numerical_computing_with_numpy_CO12-1)

逐个元素计算指数值。

[②](#co_numerical_computing_with_numpy_CO12-2)

计算每个元素的平方根。

[③](#co_numerical_computing_with_numpy_CO12-3)

计算Python `float`对象的平方根。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO12-4)

相同的计算，这次使用`math`模块。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO12-6)

`math.sqrt`不能直接应用于`ndarray`对象。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO12-7)

将通用函数`np.sqrt`应用于Python `float`对象……

[![7](images/7.png)](#co_numerical_computing_with_numpy_CO12-8)

……比使用`math.sqrt`函数的相同操作慢得多。

## 多维度

切换到多维度是无缝的，并且到目前为止呈现的所有特征都适用于更一般的情况。特别是，索引系统在所有维度上保持一致：

```py
In [53]: b = np.array([a, a * 2])  ①
         b
Out[53]: array([[  0.,   1.,   2.,   3.,   4.,   5.,   6.,   7.],
                [  0.,   2.,   4.,   6.,   8.,  10.,  12.,  14.]])

In [54]: b[0]  ②
Out[54]: array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.])

In [55]: b[0, 2]  ③
Out[55]: 2.0

In [56]: b[:, 1]  ![4](images/4.png)
Out[56]: array([ 1.,  2.])

In [57]: b.sum()  ![5](images/5.png)
Out[57]: 84.0

In [58]: b.sum(axis=0)  ![6](images/6.png)
Out[58]: array([  0.,   3.,   6.,   9.,  12.,  15.,  18.,  21.])

In [59]: b.sum(axis=1)  ![7](images/7.png)
Out[59]: array([ 28.,  56.])
```

[①](#co_numerical_computing_with_numpy_CO13-1)

用一维数组构造二维`ndarray`对象。

[②](#co_numerical_computing_with_numpy_CO13-2)

选择第一行。

[③](#co_numerical_computing_with_numpy_CO13-3)

选择第一行的第三个元素；在括号内，索引由逗号分隔。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO13-4)

选择第二列。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO13-5)

计算*所有*值的总和。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO13-6)

沿第一个轴计算总和，即按列计算。

[![7](images/7.png)](#co_numerical_computing_with_numpy_CO13-7)

沿第二轴计算总和，即按行计算。

有多种方法可以初始化（实例化）`ndarray`对象。一种方法如前所述，通过`np.array`。然而，这假定数组的所有元素已经可用。相比之下，也许我们希望首先实例化`ndarray`对象，以便在执行代码期间生成的结果后来填充它们。为此，我们可以使用以下函数：

```py
In [60]: c = np.zeros((2, 3), dtype='i', order='C')  ①
         c
Out[60]: array([[0, 0, 0],
                [0, 0, 0]], dtype=int32)

In [61]: c = np.ones((2, 3, 4), dtype='i', order='C')  ②
         c
Out[61]: array([[[1, 1, 1, 1],
                 [1, 1, 1, 1],
                 [1, 1, 1, 1]],

                [[1, 1, 1, 1],
                 [1, 1, 1, 1],
                 [1, 1, 1, 1]]], dtype=int32)

In [62]: d = np.zeros_like(c, dtype='f16', order='C')  ③
         d
Out[62]: array([[[ 0.0,  0.0,  0.0,  0.0],
                 [ 0.0,  0.0,  0.0,  0.0],
                 [ 0.0,  0.0,  0.0,  0.0]],

                [[ 0.0,  0.0,  0.0,  0.0],
                 [ 0.0,  0.0,  0.0,  0.0],
                 [ 0.0,  0.0,  0.0,  0.0]]], dtype=float128)

In [63]: d = np.ones_like(c, dtype='f16', order='C')  ③
         d
Out[63]: array([[[ 1.0,  1.0,  1.0,  1.0],
                 [ 1.0,  1.0,  1.0,  1.0],
                 [ 1.0,  1.0,  1.0,  1.0]],

                [[ 1.0,  1.0,  1.0,  1.0],
                 [ 1.0,  1.0,  1.0,  1.0],
                 [ 1.0,  1.0,  1.0,  1.0]]], dtype=float128)

In [64]: e = np.empty((2, 3, 2))  ![4](images/4.png)
         e
Out[64]: array([[[  0.00000000e+000,  -4.34540174e-311],
                 [  2.96439388e-323,   0.00000000e+000],
                 [  0.00000000e+000,   1.16095484e-028]],

                [[  2.03147708e-110,   9.67661175e-144],
                 [  9.80058441e+252,   1.23971686e+224],
                 [  4.00695466e+252,   8.34404939e-309]]])

In [65]: f = np.empty_like(c)  ![4](images/4.png)
         f
Out[65]: array([[[0, 0, 0, 0],
                 [9, 0, 0, 0],
                 [0, 0, 0, 0]],

                [[0, 0, 0, 0],
                 [0, 0, 0, 0],
                 [0, 0, 0, 0]]], dtype=int32)

In [66]: np.eye(5)  ![5](images/5.png)
Out[66]: array([[ 1.,  0.,  0.,  0.,  0.],
                [ 0.,  1.,  0.,  0.,  0.],
                [ 0.,  0.,  1.,  0.,  0.],
                [ 0.,  0.,  0.,  1.,  0.],
                [ 0.,  0.,  0.,  0.,  1.]])

In [67]: g = np.linspace(5, 15, 15) ![6](images/6.png)
         g
Out[67]: array([  5.        ,   5.71428571,   6.42857143,   7.14285714,
                  7.85714286,   8.57142857,   9.28571429,  10.        ,
                 10.71428571,  11.42857143,  12.14285714,  12.85714286,
                 13.57142857,  14.28571429,  15.        ])
```

[①](#co_numerical_computing_with_numpy_CO14-1)

用零预先填充的`ndarray`对象。

[②](#co_numerical_computing_with_numpy_CO14-2)

用1预先填充的`ndarray`对象。

[③](#co_numerical_computing_with_numpy_CO14-3)

相同，但采用另一个`ndarray`对象来推断形状。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO14-5)

`ndarray`对象不预先填充任何内容（数字取决于内存中存在的位）。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO14-7)

创建一个由1填充对角线的方阵作为`ndarray`对象。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO14-8)

创建一个一维`ndarray`对象，其中数字之间的间隔均匀分布；所使用的参数是`start`、`end`、`num`（元素数量）。

使用所有这些函数，我们可以提供以下参数：

`shape`

要么是一个`int`，一个``int`+s`序列，或者是对另一个`+numpy.ndarray`的引用

`dtype`（可选）

一个``dtype``——这些是`NumPy`特定的`numpy.ndarray`对象的数据类型

`order`（可选）

存储元素在内存中的顺序：`C`表示`C`风格（即，逐行），或`F`表示`Fortran`风格（即，逐列）

在这里，`NumPy`如何通过`ndarray`类专门构建数组的方式，与基于`list`的方法进行比较变得明显：

+   `ndarray`对象具有内置的*维度*（轴）。

+   `ndarray`对象是*不可变的*，其形状是固定的。

+   它仅允许*单一数据类型*（`numpy.dtype`）用于整个数组。

相反，`array`类只共享允许唯一数据类型（类型代码，`dtype`）的特性。

`order`参数的作用在本章稍后讨论。[表4-1](#numpy_dtypes)提供了`numpy.dtype`对象的概述（即，`NumPy`允许的基本数据类型）。

表4-1。NumPy dtype对象

| dtype | 描述 | 示例 |
| --- | --- | --- |
| `t` | 位域 | `t4` (4位) |
| `b` | 布尔 | `b`（true或false） |
| `i` | 整数 | `i8` (64位) |
| `u` | 无符号整数 | `u8` (64位) |
| `f` | 浮点数 | `f8` (64位) |
| `c` | 复数浮点数 | `c16` (128位) |
| `O` | 对象 | `0` (对象指针) |
| `S`, `a` | 字符串 | `S24` (24个字符) |
| `U` | Unicode | `U24` (24个Unicode字符) |
| `V` | 其他 | `V12` (12字节数据块) |

## 元信息

每个`ndarray`对象都提供访问一些有用属性的功能。

```py
In [68]: g.size  ①
Out[68]: 15

In [69]: g.itemsize  ②
Out[69]: 8

In [70]: g.ndim  ③
Out[70]: 1

In [71]: g.shape  ![4](images/4.png)
Out[71]: (15,)

In [72]: g.dtype  ![5](images/5.png)
Out[72]: dtype('float64')

In [73]: g.nbytes  ![6](images/6.png)
Out[73]: 120
```

[①](#co_numerical_computing_with_numpy_CO15-1)

元素的数量。

[②](#co_numerical_computing_with_numpy_CO15-2)

用于表示一个元素所使用的字节数。

[③](#co_numerical_computing_with_numpy_CO15-3)

维度的数量。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO15-4)

`ndarray`对象的形状。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO15-5)

元素的`dtype`。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO15-6)

内存中使用的总字节数。

## 重塑和调整大小

虽然`ndarray`对象默认是不可变的，但有多种选项可以重塑和调整此类对象。一般情况下，第一个操作只是提供相同数据的另一个*视图*，而第二个操作一般会创建一个*新的*（临时）对象。

```py
In [74]: g = np.arange(15)

In [75]: g
Out[75]: array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])

In [76]: g.shape  ①
Out[76]: (15,)

In [77]: np.shape(g) ①
Out[77]: (15,)

In [78]: g.reshape((3, 5))  ②
Out[78]: array([[ 0,  1,  2,  3,  4],
                [ 5,  6,  7,  8,  9],
                [10, 11, 12, 13, 14]])

In [79]: h = g.reshape((5, 3))  ③
         h
Out[79]: array([[ 0,  1,  2],
                [ 3,  4,  5],
                [ 6,  7,  8],
                [ 9, 10, 11],
                [12, 13, 14]])

In [80]: h.T  ![4](images/4.png)
Out[80]: array([[ 0,  3,  6,  9, 12],
                [ 1,  4,  7, 10, 13],
                [ 2,  5,  8, 11, 14]])

In [81]: h.transpose()  ![4](images/4.png)
Out[81]: array([[ 0,  3,  6,  9, 12],
                [ 1,  4,  7, 10, 13],
                [ 2,  5,  8, 11, 14]])
```

[①](#co_numerical_computing_with_numpy_CO16-1)

原始`ndarray`对象的形状。

[②](#co_numerical_computing_with_numpy_CO16-3)

重塑为两个维度（内存视图）。

[③](#co_numerical_computing_with_numpy_CO16-4)

创建新对象。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO16-5)

新`ndarray`对象的转置。

在重塑操作期间，`ndarray`对象中的元素总数保持不变。在调整大小操作期间，此数字会更改，即它要么减少（“向下调整”），要么增加（“向上调整”）。

```py
In [82]: g
Out[82]: array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])

In [83]: np.resize(g, (3, 1))  ①
Out[83]: array([[0],
                [1],
                [2]])

In [84]: np.resize(g, (1, 5))  ①
Out[84]: array([[0, 1, 2, 3, 4]])

In [85]: np.resize(g, (2, 5))  ①
Out[85]: array([[0, 1, 2, 3, 4],
                [5, 6, 7, 8, 9]])

In [86]: n = np.resize(g, (5, 4))  ②
         n
Out[86]: array([[ 0,  1,  2,  3],
                [ 4,  5,  6,  7],
                [ 8,  9, 10, 11],
                [12, 13, 14,  0],
                [ 1,  2,  3,  4]])
```

[①](#co_numerical_computing_with_numpy_CO17-1)

两个维度，向下调整。

[②](#co_numerical_computing_with_numpy_CO17-4)

两个维度，向上调整。

堆叠是一种特殊操作，允许水平或垂直组合两个`ndarray`对象。但是，“连接”维度的大小必须相同。

```py
In [87]: h
Out[87]: array([[ 0,  1,  2],
                [ 3,  4,  5],
                [ 6,  7,  8],
                [ 9, 10, 11],
                [12, 13, 14]])

In [88]: np.hstack((h, 2 * h))  ①
Out[88]: array([[ 0,  1,  2,  0,  2,  4],
                [ 3,  4,  5,  6,  8, 10],
                [ 6,  7,  8, 12, 14, 16],
                [ 9, 10, 11, 18, 20, 22],
                [12, 13, 14, 24, 26, 28]])

In [89]: np.vstack((h, 0.5 * h))  ②
Out[89]: array([[  0. ,   1. ,   2. ],
                [  3. ,   4. ,   5. ],
                [  6. ,   7. ,   8. ],
                [  9. ,  10. ,  11. ],
                [ 12. ,  13. ,  14. ],
                [  0. ,   0.5,   1. ],
                [  1.5,   2. ,   2.5],
                [  3. ,   3.5,   4. ],
                [  4.5,   5. ,   5.5],
                [  6. ,   6.5,   7. ]])
```

[①](#co_numerical_computing_with_numpy_CO18-1)

水平堆叠两个`ndarray`对象。

[②](#co_numerical_computing_with_numpy_CO18-2)

垂直堆叠两个`ndarray`对象。

另一个特殊操作是将多维`ndarray`对象展平为一维对象。可以选择是按行（`C`顺序）还是按列（`F`顺序）进行展平。

```py
In [90]: h
Out[90]: array([[ 0,  1,  2],
                [ 3,  4,  5],
                [ 6,  7,  8],
                [ 9, 10, 11],
                [12, 13, 14]])

In [91]: h.flatten()  ①
Out[91]: array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])

In [92]: h.flatten(order='C')  ①
Out[92]: array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14])

In [93]: h.flatten(order='F')  ②
Out[93]: array([ 0,  3,  6,  9, 12,  1,  4,  7, 10, 13,  2,  5,  8, 11, 14])

In [94]: for i in h.flat:  ③
             print(i, end=',')

         0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,

In [95]: for i in h.ravel(order='C'):  ![4](images/4.png)
             print(i, end=',')

         0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,

In [96]: for i in h.ravel(order='F'):  ![4](images/4.png)
             print(i, end=',')

         0,3,6,9,12,1,4,7,10,13,2,5,8,11,14,
```

[①](#co_numerical_computing_with_numpy_CO19-1)

平铺的默认顺序是`C`。

[②](#co_numerical_computing_with_numpy_CO19-3)

用`F`顺序展平。

[③](#co_numerical_computing_with_numpy_CO19-4)

`flat`属性提供了一个平坦的迭代器（`C`顺序）。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO19-5)

`ravel()`方法是`flatten()`的另一种选择。

## 布尔数组

比较和逻辑操作通常在`ndarray`对象上像在标准Python数据类型上一样逐元素地进行。默认情况下，评估条件会产生一个布尔`ndarray`对象（`dtype`为`bool`）。

```py
In [164]: h
Out[164]: array([[ 0,  1,  2],
                 [ 3,  4,  5],
                 [ 6,  7,  8],
                 [ 9, 10, 11],
                 [12, 13, 14]])

In [150]: h > 8  ①
Out[150]: array([[False, False, False],
                 [False, False, False],
                 [False, False, False],
                 [ True,  True,  True],
                 [ True,  True,  True]], dtype=bool)

In [151]: h <= 7  ②
Out[151]: array([[ True,  True,  True],
                 [ True,  True,  True],
                 [ True,  True, False],
                 [False, False, False],
                 [False, False, False]], dtype=bool)

In [152]: h == 5  ③
Out[152]: array([[False, False, False],
                 [False, False,  True],
                 [False, False, False],
                 [False, False, False],
                 [False, False, False]], dtype=bool)

In [158]: (h == 5).astype(int)  ![4](images/4.png)
Out[158]: array([[0, 0, 0],
                 [0, 0, 1],
                 [0, 0, 0],
                 [0, 0, 0],
                 [0, 0, 0]])

In [165]: (h > 4) & (h <= 12)  ![5](images/5.png)
Out[165]: array([[False, False, False],
                 [False, False,  True],
                 [ True,  True,  True],
                 [ True,  True,  True],
                 [ True, False, False]], dtype=bool)
```

[①](#co_numerical_computing_with_numpy_CO20-1)

值是否大于...？

[②](#co_numerical_computing_with_numpy_CO20-2)

值是否小于或等于...？

[③](#co_numerical_computing_with_numpy_CO20-3)

值是否等于...？

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO20-4)

以整数值0和1表示`True`和`False`。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO20-5)

值是否大于...且小于或等于...？

此类布尔数组可用于索引和数据选择。注意以下操作会展平数据。

```py
In [153]: h[h > 8]  ①
Out[153]: array([ 9, 10, 11, 12, 13, 14])

In [155]: h[(h > 4) & (h <= 12)]  ②
Out[155]: array([ 5,  6,  7,  8,  9, 10, 11, 12])

In [157]: h[(h < 4) | (h >= 12)]  ③
Out[157]: array([ 0,  1,  2,  3, 12, 13, 14])
```

[①](#co_numerical_computing_with_numpy_CO21-1)

给我所有大于...的值。

[②](#co_numerical_computing_with_numpy_CO21-2)

给我所有大于... *且*小于或等于...的值。

[③](#co_numerical_computing_with_numpy_CO21-3)

给我所有大于... *或*小于或等于...的值。

在这方面的一个强大工具是`np.where()`函数，它允许根据条件是`True`还是`False`来定义操作/操作。应用`np.where()`的结果是一个与原始对象相同形状的新`ndarray`对象。

```py
In [159]: np.where(h > 7, 1, 0)  ①
Out[159]: array([[0, 0, 0],
                 [0, 0, 0],
                 [0, 0, 1],
                 [1, 1, 1],
                 [1, 1, 1]])

In [160]: np.where(h % 2 == 0, 'even', 'odd')  ②
Out[160]: array([['even', 'odd', 'even'],
                 ['odd', 'even', 'odd'],
                 ['even', 'odd', 'even'],
                 ['odd', 'even', 'odd'],
                 ['even', 'odd', 'even']],
                dtype='<U4')

In [163]: np.where(h <= 7, h * 2, h / 2)  ③
Out[163]: array([[  0. ,   2. ,   4. ],
                 [  6. ,   8. ,  10. ],
                 [ 12. ,  14. ,   4. ],
                 [  4.5,   5. ,   5.5],
                 [  6. ,   6.5,   7. ]])
```

[①](#co_numerical_computing_with_numpy_CO22-1)

在新对象中，如果为`True`，则设置为`1`，否则设置为`0`。

[②](#co_numerical_computing_with_numpy_CO22-2)

在新对象中，如果为`True`，则设置为`even`，否则设置为`odd`。

[③](#co_numerical_computing_with_numpy_CO22-3)

在新对象中，如果为`True`，则将`h`元素设置为两倍，否则将`h`元素设置为一半。

后续章节提供了关于`ndarray`对象上这些重要操作的更多示例。

## 速度比较

在转向具有`NumPy`的结构化数组之前，让我们暂时保持常规数组，并看看专业化在性能方面带来了什么。

以一个简单的例子为例，假设我们想要生成一个形状为 5,000 × 5,000 元素的矩阵/数组，填充了（伪）随机的标准正态分布的数字。然后我们想要计算所有元素的总和。首先，纯`Python`方法，我们使用`list`推导来实现：

```py
In [97]: import random
         I = 5000

In [98]: %time mat = [[random.gauss(0, 1) for j in range(I)] \
                      for i in range(I)]  ①

         CPU times: user 20.9 s, sys: 372 ms, total: 21.3 s
         Wall time: 21.3 s

In [99]: mat[0][:5]  ②
Out[99]: [0.02023704728430644,
          -0.5773300286314157,
          -0.5034574089604074,
          -0.07769332062744054,
          -0.4264012594572326]

In [100]: %time sum([sum(l) for l in mat])  ③

          CPU times: user 156 ms, sys: 1.93 ms, total: 158 ms
          Wall time: 158 ms

Out[100]: 681.9120404070142

In [101]: import sys
          sum([sys.getsizeof(l) for l in mat])  ![4](images/4.png)
Out[101]: 215200000
```

[①](#co_numerical_computing_with_numpy_CO23-1)

通过嵌套的列表推导来创建矩阵。

[②](#co_numerical_computing_with_numpy_CO23-2)

从所绘制的数字中选择一些随机数。

[③](#co_numerical_computing_with_numpy_CO23-3)

首先在列表推导中计算单个`list`对象的总和；然后计算总和的总和。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO23-4)

添加所有`list`对象的内存使用量。

现在让我们转向`NumPy`，看看同样的问题是如何在那里解决的。为了方便，`NumPy`子库`random`提供了许多函数来实例化一个`ndarray`对象，并同时填充它（伪）随机数：

```py
In [102]: %time mat = np.random.standard_normal((I, I))  ①

          CPU times: user 1.14 s, sys: 170 ms, total: 1.31 s
          Wall time: 1.32 s

In [103]: %time mat.sum()  ②

          CPU times: user 29.5 ms, sys: 1.32 ms, total: 30.8 ms
          Wall time: 29.7 ms

Out[103]: 2643.0006104377485

In [104]: mat.nbytes  ③
Out[104]: 200000000

In [105]: sys.getsizeof(mat)  ③
Out[105]: 200000112
```

[①](#co_numerical_computing_with_numpy_CO24-1)

使用标准正态分布的随机数字创建`ndarray`对象；速度约快20倍。

[②](#co_numerical_computing_with_numpy_CO24-2)

计算`ndarray`对象中所有值的总和；速度约快6倍。

[③](#co_numerical_computing_with_numpy_CO24-3)

`NumPy`方法也节省了一些内存，因为`ndarray`对象的内存开销与数据本身的大小相比微不足道。

我们观察到以下情况：

语法

尽管我们使用了几种方法来压缩纯`Python`代码，但`NumPy`版本更加紧凑和易读。

性能

生成`ndarray`对象的速度大约快了20倍，求和的计算速度大约快了6倍，比纯`Python`中的相应操作更快。

# 使用NumPy数组

使用`NumPy`进行基于数组的操作和算法通常会导致代码紧凑、易读，并且与纯`Python`代码相比具有显著的性能改进。

# 结构化NumPy数组

`ndarray`类的专业化显然带来了许多有价值的好处。然而，太窄的专业化可能对大多数基于数组的算法和应用程序来说是一个太大的负担。因此，`NumPy`提供了允许每列具有不同`dtype`的*结构化*或*记录*`ndarray`对象。什么是“每列”？考虑以下结构化数组对象的初始化：

```py
In [106]: dt = np.dtype([('Name', 'S10'), ('Age', 'i4'),
                         ('Height', 'f'), ('Children/Pets', 'i4', 2)])  ①

In [107]: dt  ①
Out[107]: dtype([('Name', 'S10'), ('Age', '<i4'), ('Height', '<f4'), ('Children/Pets', '<i4', (2,))])

In [108]: dt = np.dtype({'names': ['Name', 'Age', 'Height', 'Children/Pets'],
                       'formats':'O int float int,int'.split()})  ②

In [109]: dt  ②
Out[109]: dtype([('Name', 'O'), ('Age', '<i8'), ('Height', '<f8'), ('Children/Pets', [('f0', '<i8'), ('f1', '<i8')])])

In [110]: s = np.array([('Smith', 45, 1.83, (0, 1)),
                        ('Jones', 53, 1.72, (2, 2))], dtype=dt)  ③

In [111]: s  ③
Out[111]: array([('Smith', 45,  1.83, (0, 1)), ('Jones', 53,  1.72, (2, 2))],
                dtype=[('Name', 'O'), ('Age', '<i8'), ('Height', '<f8'), ('Children/Pets', [('f0', '<i8'), ('f1', '<i8')])])

In [112]: type(s)  ![4](images/4.png)
Out[112]: numpy.ndarray
```

[①](#co_numerical_computing_with_numpy_CO25-1)

复杂的`dtype`是由几部分组成的。

[②](#co_numerical_computing_with_numpy_CO25-3)

实现相同结果的替代语法。

[③](#co_numerical_computing_with_numpy_CO25-5)

结构化`ndarray`以两条记录实例化。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO25-7)

对象类型仍然是`numpy.ndarray`。

从某种意义上说，这个构造与初始化`SQL`数据库中的表格的操作非常接近。我们有列名和列数据类型，可能还有一些附加信息（例如，每个`string`对象的最大字符数）。现在可以通过它们的名称轻松访问单个列，并通过它们的索引值访问行：

```py
In [113]: s['Name']  ①
Out[113]: array(['Smith', 'Jones'], dtype=object)

In [114]: s['Height'].mean()  ②
Out[114]: 1.7749999999999999

In [115]: s[0]  ③
Out[115]: ('Smith', 45,  1.83, (0, 1))

In [116]: s[1]['Age']  ![4](images/4.png)
Out[116]: 53
```

[①](#co_numerical_computing_with_numpy_CO26-1)

通过名称选择一列。

[②](#co_numerical_computing_with_numpy_CO26-2)

在选定的列上调用方法。

[③](#co_numerical_computing_with_numpy_CO26-3)

选择一条记录。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO26-4)

选择记录中的一个字段。

总之，结构化数组是常规`numpy.ndarray`对象类型的泛化，因为数据类型只需在*每列*上保持相同，就像在`SQL`数据库表格上的上下文中一样。结构化数组的一个优点是，列的单个元素可以是另一个多维对象，不必符合基本的`NumPy`数据类型。

# 结构化数组

`NumPy`提供了除了常规数组之外，还提供了结构化（记录）数组，允许描述和处理类似表格的数据结构，每个（命名的）列具有各种不同的数据类型。它们将`SQL`表格类似的数据结构带到了`Python`中，大部分具备常规`ndarray`对象的优点（语法、方法、性能）。

# 代码的向量化

代码的矢量化是一种获得更紧凑代码并可能更快执行的策略。其基本思想是对复杂对象进行“一次性”操作或应用函数，而不是通过循环遍历对象的单个元素。在`Python`中，函数式编程工具，如`map`和`filter`，提供了一些基本的矢量化手段。然而，`NumPy`在其核心深处内置了矢量化。

## 基本矢量化

正如我们在上一节中学到的，简单的数学运算，如计算所有元素的总和，可以直接在`ndarray`对象上实现（通过方法或通用函数）。还可以进行更一般的矢量化操作。例如，我们可以按元素将两个`NumPy`数组相加如下：

```py
In [117]: np.random.seed(100)
          r = np.arange(12).reshape((4, 3))  ①
          s = np.arange(12).reshape((4, 3)) * 0.5  ②

In [118]: r  ①
Out[118]: array([[ 0,  1,  2],
                 [ 3,  4,  5],
                 [ 6,  7,  8],
                 [ 9, 10, 11]])

In [119]: s  ②
Out[119]: array([[ 0. ,  0.5,  1. ],
                 [ 1.5,  2. ,  2.5],
                 [ 3. ,  3.5,  4. ],
                 [ 4.5,  5. ,  5.5]])

In [120]: r + s  ③
Out[120]: array([[  0. ,   1.5,   3. ],
                 [  4.5,   6. ,   7.5],
                 [  9. ,  10.5,  12. ],
                 [ 13.5,  15. ,  16.5]])
```

[①](#co_numerical_computing_with_numpy_CO27-1)

具有随机数的第一个`ndarray`对象。

[②](#co_numerical_computing_with_numpy_CO27-2)

具有随机数的第二个`ndarray`对象。

[③](#co_numerical_computing_with_numpy_CO27-5)

逐元素加法作为矢量化操作（无循环）。

`NumPy`还支持所谓的*广播*。这允许在单个操作中组合不同形状的对象。我们之前已经使用过这个功能。考虑以下示例：

```py
In [121]: r + 3  ①
Out[121]: array([[ 3,  4,  5],
                 [ 6,  7,  8],
                 [ 9, 10, 11],
                 [12, 13, 14]])

In [122]: 2 * r  ②
Out[122]: array([[ 0,  2,  4],
                 [ 6,  8, 10],
                 [12, 14, 16],
                 [18, 20, 22]])

In [123]: 2 * r + 3  ③
Out[123]: array([[ 3,  5,  7],
                 [ 9, 11, 13],
                 [15, 17, 19],
                 [21, 23, 25]])
```

[①](#co_numerical_computing_with_numpy_CO28-1)

在标量加法期间，标量被广播并添加到每个元素。

[②](#co_numerical_computing_with_numpy_CO28-2)

在标量乘法期间，标量也广播并与每个元素相乘。

[③](#co_numerical_computing_with_numpy_CO28-3)

此线性变换结合了两个操作。

这些操作也适用于不同形状的`ndarray`对象，直到某个特定点为止：

```py
In [124]: r
Out[124]: array([[ 0,  1,  2],
                 [ 3,  4,  5],
                 [ 6,  7,  8],
                 [ 9, 10, 11]])

In [125]: r.shape
Out[125]: (4, 3)

In [126]: s = np.arange(0, 12, 4)  ①
          s  ①
Out[126]: array([0, 4, 8])

In [127]: r + s  ②
Out[127]: array([[ 0,  5, 10],
                 [ 3,  8, 13],
                 [ 6, 11, 16],
                 [ 9, 14, 19]])

In [128]: s = np.arange(0, 12, 3)  ③
          s  ③
Out[128]: array([0, 3, 6, 9])

In [129]: # r + s ![4](images/4.png)

In [130]: r.transpose() + s  ![5](images/5.png)
Out[130]: array([[ 0,  6, 12, 18],
                 [ 1,  7, 13, 19],
                 [ 2,  8, 14, 20]])

In [131]: sr = s.reshape(-1, 1)  ![6](images/6.png)
          sr
Out[131]: array([[0],
                 [3],
                 [6],
                 [9]])

In [132]: sr.shape  ![6](images/6.png)
Out[132]: (4, 1)

In [133]: r + s.reshape(-1, 1)  ![6](images/6.png)
Out[133]: array([[ 0,  1,  2],
                 [ 6,  7,  8],
                 [12, 13, 14],
                 [18, 19, 20]])
```

[①](#co_numerical_computing_with_numpy_CO29-1)

长度为3的新一维`ndarray`对象。

[②](#co_numerical_computing_with_numpy_CO29-3)

`r`（矩阵）和`s`（向量）对象可以直接相加。

[③](#co_numerical_computing_with_numpy_CO29-4)

另一个长度为4的一维`ndarray`对象。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO29-6)

新`s`（向量）对象的长度现在与`r`对象的第二维长度不同。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO29-7)

再次转置`r`对象允许进行矢量化加法。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO29-8)

或者，`s`的形状可以更改为`(4, 1)`以使加法起作用（但结果不同）。

通常情况下，自定义的`Python`函数也适用于`numpy.ndarray`。如果实现允许，数组可以像`int`或`float`对象一样与函数一起使用。考虑以下函数：

```py
In [134]: def f(x):
              return 3 * x + 5  ①

In [135]: f(0.5)  ②
Out[135]: 6.5

In [136]: f(r)  ③
Out[136]: array([[ 5,  8, 11],
                 [14, 17, 20],
                 [23, 26, 29],
                 [32, 35, 38]])
```

[①](#co_numerical_computing_with_numpy_CO30-1)

实现对参数`x`进行线性变换的简单Python函数。

[②](#co_numerical_computing_with_numpy_CO30-2)

函数`f`应用于Python的`float`对象。

[③](#co_numerical_computing_with_numpy_CO30-3)

同一函数应用于`ndarray`对象，导致函数的向量化和逐个元素的评估。

`NumPy`所做的是简单地将函数`f`逐个元素地应用于对象。在这种意义上，通过使用这种操作，我们并*不*避免循环；我们只是在`Python`级别上避免了它们，并将循环委托给了`NumPy`。在`NumPy`级别上，对`ndarray`对象进行循环处理是由高度优化的代码来完成的，其中大部分代码都是用`C`编写的，因此通常比纯`Python`快得多。这解释了在基于数组的用例中使用`NumPy`带来性能优势的“秘密”。

## 内存布局

当我们首次使用`np.zero`初始化`numpy.ndarray`对象时，我们提供了一个可选参数用于内存布局。这个参数大致指定了数组的哪些元素会被连续地存储在内存中。当处理小数组时，这几乎不会对数组操作的性能产生任何可测量的影响。然而，当数组变大并且取决于要在其上实现的（财务）算法时，情况可能会有所不同。这就是*内存布局*发挥作用的时候（参见，例如[多维数组的内存布局](https://eli.thegreenplace.net/2015/memory-layout-of-multi-dimensional-arrays/)）。

要说明数组的内存布局在科学和金融中的潜在重要性，考虑以下构建多维`ndarray`对象的情况：

```py
In [137]: x = np.random.standard_normal((1000000, 5))  ①

In [138]: y = 2 * x + 3  ②

In [139]: C = np.array((x, y), order='C')  ③

In [140]: F = np.array((x, y), order='F')  ![4](images/4.png)

In [141]: x = 0.0; y = 0.0  ![5](images/5.png)

In [142]: C[:2].round(2)  ![6](images/6.png)
Out[142]: array([[[-1.75,  0.34,  1.15, -0.25,  0.98],
                  [ 0.51,  0.22, -1.07, -0.19,  0.26],
                  [-0.46,  0.44, -0.58,  0.82,  0.67],
                  ...,
                  [-0.05,  0.14,  0.17,  0.33,  1.39],
                  [ 1.02,  0.3 , -1.23, -0.68, -0.87],
                  [ 0.83, -0.73,  1.03,  0.34, -0.46]],

                 [[-0.5 ,  3.69,  5.31,  2.5 ,  4.96],
                  [ 4.03,  3.44,  0.86,  2.62,  3.51],
                  [ 2.08,  3.87,  1.83,  4.63,  4.35],
                  ...,
                  [ 2.9 ,  3.28,  3.33,  3.67,  5.78],
                  [ 5.04,  3.6 ,  0.54,  1.65,  1.26],
                  [ 4.67,  1.54,  5.06,  3.69,  2.07]]])
```

[①](#co_numerical_computing_with_numpy_CO31-1)

一个在两个维度上具有较大不对称性的`ndarray`对象。

[②](#co_numerical_computing_with_numpy_CO31-2)

对原始对象数据进行线性变换。

[③](#co_numerical_computing_with_numpy_CO31-3)

这将创建一个二维`ndarray`对象，其顺序为`C`（行优先）。

[![4](images/4.png)](#co_numerical_computing_with_numpy_CO31-4)

这将创建一个二维`ndarray`对象，其顺序为`F`（列优先）。

[![5](images/5.png)](#co_numerical_computing_with_numpy_CO31-5)

内存被释放（取决于垃圾收集）。

[![6](images/6.png)](#co_numerical_computing_with_numpy_CO31-6)

从`C`对象中获取一些数字。

让我们看一些关于两种类型的`ndarray`对象的基本示例和用例，并考虑它们在不同内存布局下执行的速度：

```py
In [143]: %timeit C.sum()  ①

          4.65 ms ± 73.3 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

In [144]: %timeit F.sum()  ①

          4.56 ms ± 105 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

In [145]: %timeit C.sum(axis=0)  ②

          20.9 ms ± 358 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)

In [146]: %timeit C.sum(axis=1)  ③

          38.5 ms ± 1.1 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)

In [147]: %timeit F.sum(axis=0)  ②

          87.5 ms ± 1.37 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)

In [148]: %timeit F.sum(axis=1)  ③

          81.6 ms ± 1.66 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)

In [149]: F = 0.0; C = 0.0
```

[①](#co_numerical_computing_with_numpy_CO32-1)

计算所有元素的总和。

[②](#co_numerical_computing_with_numpy_CO32-3)

每行计算和（“许多”）。

[③](#co_numerical_computing_with_numpy_CO32-4)

计算每列的总和（“少”）。

我们可以总结性能结果如下：

+   当计算*所有元素*的总和时，内存布局实际上并不重要。

+   对 `C`-ordered `ndarray` 对象的求和在行和列上都更快（*绝对*速度优势）。

+   使用 `C`-ordered（行优先）`ndarray` 对象，对行求和*相对*比对列求和更快。

+   使用 `F`-ordered（列优先）`ndarray` 对象，对列求和*相对*比对行求和更快。

# 结论

`NumPy` 是 Python 中数值计算的首选包。`ndarray` 类是专门设计用于处理（大）数值数据的高效方便的类。强大的方法和 `NumPy` 的通用函数允许进行向量化的代码，大部分避免了在 Python 层上的慢循环。本章介绍的许多方法也适用于 `pandas` 及其 `DataFrame` 类（见 [第5章](ch05.html#pandas)）

# 更多资源

有用的资源提供在：

+   [*http://www.numpy.org/*](http://www.numpy.org/)

优秀的 `NumPy` 介绍书籍包括：

+   McKinney, Wes（2017）：*Python 数据分析*。第2版，O’Reilly，北京等。

+   VanderPlas, Jake（2016）：*Python 数据科学手册*。O’Reilly，北京等。
