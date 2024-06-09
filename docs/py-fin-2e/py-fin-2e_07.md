# 第七章：第 7 章 数据可视化

> 使用图片。一图胜千言。
> 
> 阿瑟·布里斯班（1911年）

本章介绍了[`matplotlib`](http://www.matplotlib.org)和[`plotly`](http://plot.ly)库的基本可视化能力。

尽管有许多其他可用的可视化库，但`matplotlib`已经确立了自己作为基准，并且在许多情况下是一个强大而可靠的可视化工具。在标准绘图方面易于使用，在更复杂的绘图和定制方面灵活。此外，它与`NumPy`和`pandas`及它们提供的数据结构紧密集成。

`matplotlib`仅允许以位图形式（例如PNG或JPG格式）生成图。另一方面，现代网络技术允许基于[数据驱动文档（D3.js）](https://d3js.org/)标准创建漂亮的交互式图表，例如，可以缩放以更详细地检查某些区域。一个非常方便的库，可以使用 Python 创建这样的 D3.js 图表，是`plotly`。一个小的附加库，称为`Cufflinks`，将`plotly`与`pandas`的`DataFrame`对象紧密集成，可以创建最受欢迎的金融图表（如蜡烛图）

本章主要涵盖以下主题：

[“静态 2D 绘图”](#viz_2d_plotting)

本节介绍了`matplotlib`，并呈现了一些典型的 2D 绘图，从最简单的到具有两个比例尺或不同子图的更高级的绘图。

[“静态 3D 绘图”](#viz_3d_plotting)

基于`matplotlib`，介绍了一些在特定金融应用中有用的 3D 绘图。

[“交互式 2D 绘图”](#viz_int_2d_plotting)

本节介绍了`plotly`和`Cufflinks`，用于创建交互式 2D 绘图。利用`Cufflinks`的`QuantFigure`功能，本节还涉及典型的金融绘图，例如在技术股票分析中使用的绘图。

本章无法全面涵盖使用`Python`、`matplotlib`或`plotly`进行数据可视化的所有方面，但它提供了这些包在金融领域的基本和重要功能的一些示例。其他示例也可以在后面的章节中找到。例如，[第 8 章](ch08.html#fin_time_series)更深入地介绍了如何使用`pandas`库可视化金融时间序列数据。

# 静态 2D 绘图

在创建样本数据并开始绘图之前，首先进行一些导入和自定义：

```py
In [1]: import matplotlib as mpl  ![1](images/1.png)

In [2]: mpl.__version__  ![2](images/2.png)
Out[2]: '2.0.2'

In [3]: import matplotlib.pyplot as plt  ![4](images/4.png)

In [4]: plt.style.use('seaborn')  ![5](images/5.png)

In [5]: mpl.rcParams['font.family'] = 'serif'  ![3](images/3.png)

In [6]: %matplotlib inline
```

[![1](images/1.png)](#co_financial_data_science_CO1-1)

使用常见缩写`mpl`导入了`matplotlib`。

[![2](images/2.png)](#co_financial_data_science_CO1-2)

使用的`matplotlib`版本。

[![3](images/3.png)](#co_financial_data_science_CO1-5)

将所有图的字体设置为`serif`。

[![4](images/4.png)](#co_financial_data_science_CO1-3)

使用常见缩写`plt`导入了主要的绘图（子）包。

[![5](images/5.png)](#co_financial_data_science_CO1-4)

将绘图样式设置为`seaborn`（请参阅，例如，[此处](https://tonysyu.github.io/raw_content/matplotlib-style-gallery/gallery.html)的概述）。

## 一维数据集

在接下来的所有内容中，我们将绘制存储在`NumPy`的`ndarray`对象或`pandas`的`DataFrame`对象中的数据。然而，`matplotlib`当然也能够绘制存储在不同`Python`格式中的数据，比如`list`对象。最基本但相当强大的绘图函数是`plt.plot()`。原则上，它需要两组数字：

+   **``x`` 值**：包含``x``坐标（横坐标值）的列表或数组

+   **``y`` 值**：包含``y``坐标（纵坐标值）的列表或数组

提供的``x``和``y``值的数量必须相匹配，当然了。考虑下面的代码，其输出如[图 7-1](#matplotlib_0)所示。

```py
In [7]: import numpy as np

In [8]: np.random.seed(1000)  ![1](images/1.png)

In [9]: y = np.random.standard_normal(20)  ![2](images/2.png)

In [10]: x = np.arange(len(y))  ![3](images/3.png)
         plt.plot(x, y);  ![4](images/4.png)
         # plt.savefig('../../images/ch07/mpl_01')
```

[![1](images/1.png)](#co_financial_data_science_CO2-1)

为了可重复性，设置随机数生成器的种子。

[![2](images/2.png)](#co_financial_data_science_CO2-2)

绘制随机数（y值）。

[![3](images/3.png)](#co_financial_data_science_CO2-3)

固定整数（x值）。

[![4](images/4.png)](#co_financial_data_science_CO2-4)

使用`x`和`y`对象调用`plt.plot()`函数。

![mpl 01](images/mpl_01.png)

###### 图 7-1\. 绘制给定的x和y值

`plt.plot()`注意到当您传递一个`ndarray`对象时。在这种情况下，无需提供``x``值的“额外”信息。如果您只提供``y``值，则`plot`将索引值视为相应的``x``值。因此，以下单行代码生成完全相同的输出（参见[图 7-2](#matplotlib_1)）：

```py
In [11]: plt.plot(y);
         # plt.savefig('../../images/ch07/mpl_02')
```

![mpl 02](images/mpl_02.png)

###### 图 7-2\. 绘制给定的`ndarray`对象的数据

# NumPy数组和matplotlib

您可以简单地将`NumPy`的`ndarray`对象传递给`matplotlib`函数。`matplotlib`能够解释数据结构以简化绘图。但是，请注意不要传递过大和/或复杂的数组。

由于大多数`ndarray`方法再次返回一个`ndarray`对象，因此您还可以通过附加方法（甚至在某些情况下可以是多个方法）来传递您的对象。通过在样本数据上调用`cumsum()`方法，我们得到了这些数据的累积和，正如预期的那样，得到了不同的输出（参见[图 7-3](#matplotlib_2)）：

```py
In [12]: plt.plot(y.cumsum());
         # plt.savefig('../../images/ch07/mpl_03')
```

![mpl 03](images/mpl_03.png)

###### 图 7-3\. 绘制给定一个带有附加方法的`ndarray`对象

通常，默认的绘图样式不能满足报告、出版物等的典型要求。例如，您可能希望自定义使用的字体（例如，与`LaTeX`字体兼容），在轴上标记标签，或者绘制网格以提高可读性。这就是绘图样式发挥作用的地方（见上文）。此外，`matplotlib`提供了大量函数来自定义绘图样式。有些函数很容易访问；对于其他一些函数，需要深入挖掘。例如，很容易访问的是那些操作轴的函数以及与网格和标签相关的函数（参见[图 7-4](#matplotlib_3_a)）：

```py
In [13]: plt.plot(y.cumsum())
         plt.grid(False);  ![1](images/1.png)
         # plt.savefig('../../images/ch07/mpl_04')
```

[![1](images/1.png)](#co_financial_data_science_CO3-1)

关闭网格。

![mpl 04](images/mpl_04.png)

###### 图 7-4。没有网格的图

`plt.axis()`的其他选项在表 7-1中给出，其中大多数必须作为`string`对象传递。

表 7-1。plt.axis()的选项

| 参数 | 描述 |
| --- | --- |
| 空 | 返回当前轴限制 |
| `off` | 关闭轴线和标签 |
| `equal` | 导致等比例缩放 |
| `scaled` | 通过尺寸变化实现等比例缩放 |
| `tight` | 使所有数据可见（紧缩限制） |
| `image` | 使所有数据可见（带有数据限制） |
| `[xmin, xmax, ymin, ymax]` | 设置给定（列表的）值的限制 |

此外，您可以直接使用`plt.xlim()`和`plt.ylim()`设置每个轴的最小和最大值。以下代码提供了一个示例，其输出显示在[图 7-5](#matplotlib_3_b)中：

```py
In [14]: plt.plot(y.cumsum())
         plt.xlim(-1, 20)
         plt.ylim(np.min(y.cumsum()) - 1,
                  np.max(y.cumsum()) + 1);
         # plt.savefig('../../images/ch07/mpl_05')
```

![mpl 05](images/mpl_05.png)

###### 图 7-5。带有自定义轴限制的图

为了更好地可读性，图表通常包含许多标签，例如标题和描述``x``和``y``值性质的标签。这些分别通过函数`plt.title`、`plt.xlabel`和`plt.ylabel`添加。默认情况下，`plot`绘制连续线条，即使提供了离散数据点。通过选择不同的样式选项来绘制离散点。[图 7-6](#matplotlib_4)叠加了（红色）点和（蓝色）线，线宽为1.5点：

```py
In [15]: plt.figure(figsize=(10, 6))  ![1](images/1.png)
         plt.plot(y.cumsum(), 'b', lw=1.5)  ![2](images/2.png)
         plt.plot(y.cumsum(), 'ro')  ![3](images/3.png)
         plt.xlabel('index')  ![4](images/4.png)
         plt.ylabel('value')  ![5](images/5.png)
         plt.title('A Simple Plot');  ![6](images/6.png)
         # plt.savefig('../../images/ch07/mpl_06')
```

[![1](images/1.png)](#co_financial_data_science_CO4-1)

增加图的大小。

[![2](images/2.png)](#co_financial_data_science_CO4-2)

将数据绘制为蓝色线条，线宽为1.5点。

[![3](images/3.png)](#co_financial_data_science_CO4-3)

将数据绘制为红色（粗）点。

[![4](images/4.png)](#co_financial_data_science_CO4-4)

在x轴上放置一个标签。

[![5](images/5.png)](#co_financial_data_science_CO4-5)

在y轴上放置一个标签。

[![6](images/6.png)](#co_financial_data_science_CO4-6)

放置一个标题。

![mpl 06](images/mpl_06.png)

###### 图 7-6。具有典型标签的图

默认情况下，`plt.plot()`支持[表 7-2](#color_tab)中的颜色缩写。

表 7-2。标准颜色缩写

| 字符 | 颜色 |
| --- | --- |
| `b` | 蓝色 |
| `g` | 绿色 |
| `r` | 红色 |
| `c` | 青色 |
| `m` | 紫红色 |
| `y` | 黄色 |
| `k` | 黑色 |
| `w` | 白色 |

在线和/或点样式方面，`plt.plot()`支持[表 7-3](#style_tab)中列出的字符。

表7-3. 标准样式字符

| 字符 | 符号 |
| --- | --- |
| `-` | 实线型 |
| `--` | 虚线型 |
| `-.` | 短划线-点线型 |
| `:` | 点线型 |
| `.` | 点标记 |
| `,` | 像素标记 |
| `o` | 圆形标记 |
| `v` | 向下三角形标记 |
| `0` | 向上三角形标记 |
| `<` | 向左三角形标记 |
| `>` | 向右三角形标记 |
| `1` | 向下三角形标记 |
| `2` | 向上三角形标记 |
| `3` | 向左三角形标记 |
| `4` | 向右三角形标记 |
| `s` | 正方形标记 |
| `p` | 五边形标记 |
| `0` | 星形标记 |
| `h` | 六边形1 标记 |
| `H` | 六边形2 标记 |
| `0` | 加号标记 |
| `x` | X 标记 |
| `D` | 菱形标记 |
| `d` | 窄菱形标记 |
| `pass:[ | ]` |
| 垂直线标记 | `0` |

任何颜色缩写都可以与任何样式字符组合。通过这种方式，您可以确保不同的数据集易于区分。正如我们将看到的，绘图样式也将反映在图例中。

## 二维数据集

绘制一维数据可以被视为一种特殊情况。一般来说，数据集将由多个单独的数据子集组成。与 `matplotlib` 一维数据一样，处理这样的数据集遵循相同的规则。但是，在这种情况下可能会出现一些额外的问题。例如，两个数据集的缩放可能有如此之大的不同，以至于不能使用相同的y轴和/或x轴缩放绘制它们。另一个问题可能是您可能希望以不同的方式可视化两个不同的数据集，例如，通过线图绘制一个数据集，通过条形图绘制另一个数据集。

以下代码生成一个具有20×2形状的标准正态分布（伪随机）数字的`NumPy` `ndarray`对象的二维样本数据集。对这个数组，调用`cumsum()`方法来计算样本数据沿轴0（即第一个维度）的累积和：

```py
In [16]: y = np.random.standard_normal((20, 2)).cumsum(axis=0)
```

一般来说，您也可以将这样的二维数组传递给 `plt.plot`。然后，它将自动解释包含的数据为单独的数据集（沿着轴1，即第二个维度）。相应的图示显示在[图 7-7](#matplotlib_5)中：

```py
In [17]: plt.figure(figsize=(10, 6))
         plt.plot(y, lw=1.5)
         plt.plot(y, 'ro')
         plt.xlabel('index')
         plt.ylabel('value')
         plt.title('A Simple Plot');
         # plt.savefig('../../images/ch07/mpl_07')
```

![mpl 07](images/mpl_07.png)

###### 图7-7. 带有两个数据集的图

在这种情况下，进一步的注释可能有助于更好地阅读图表。您可以为每个数据集添加单独的标签，并在图例中列出它们。 `plt.legend()` 接受不同的位置参数。`0` 代表*最佳位置*，意味着图例尽可能少地遮挡数据。[图 7-8](#matplotlib_6) 展示了两个数据集的图表，这次有了图例。在生成的代码中，我们现在不再将 `ndarray` 对象作为一个整体传递，而是分别访问两个数据子集（`y[:, 0]` 和 `y[:, 0]`），这样可以为它们附加单独的标签：

```py
In [18]: plt.figure(figsize=(10, 6))
         plt.plot(y[:, 0], lw=1.5, label='1st')  ![1](images/1.png)
         plt.plot(y[:, 1], lw=1.5, label='2nd')  ![1](images/1.png)
         plt.plot(y, 'ro')
         plt.legend(loc=0)  ![2](images/2.png)
         plt.xlabel('index')
         plt.ylabel('value')
         plt.title('A Simple Plot');
         # plt.savefig('../../images/ch07/mpl_08')
```

[![1](images/1.png)](#co_financial_data_science_CO5-1)

为数据子集定义标签。

[![2](images/2.png)](#co_financial_data_science_CO5-3)

将图例放在*最佳*位置。

进一步的 `plt.legend()` 位置选项包括 [表 7-4](#legend_opts) 中介绍的选项。

表 7-4\. `plt.legend()` 的选项

| 位置 | 描述 |
| --- | --- |
| 空 | 自动 |
| `0` | 最佳位置 |
| `1` | 右上角 |
| `2` | 左上角 |
| `3` | 左下角 |
| `4` | 右下角 |
| `5` | 右 |
| `6` | 左中 |
| `7` | 右中 |
| `8` | 底部中心 |
| `9` | 上部中心 |
| `10` | 中心 |

![mpl 08](images/mpl_08.png)

###### 图 7-8\. 带标记数据集的图表

具有相似缩放的多个数据集，例如相同财务风险因素的模拟路径，可以使用单个 y 轴绘制。然而，通常数据集显示的缩放相差较大，并且使用单个 y 轴绘制此类数据通常会导致视觉信息的严重丢失。为了说明效果，我们将两个数据子集中的第一个缩放因子放大了 100 倍，并再次绘制数据（参见 [图 7-9](#matplotlib_7)）：

```py
In [19]: y[:, 0] = y[:, 0] * 100  ![1](images/1.png)

In [20]: plt.figure(figsize=(10, 6))
         plt.plot(y[:, 0], lw=1.5, label='1st')
         plt.plot(y[:, 1], lw=1.5, label='2nd')
         plt.plot(y, 'ro')
         plt.legend(loc=0)
         plt.xlabel('index')
         plt.ylabel('value')
         plt.title('A Simple Plot');
         # plt.savefig('../../images/ch07/mpl_09')
```

[![1](images/1.png)](#co_financial_data_science_CO6-1)

重新调整第一个数据子集的比例。

![mpl 09](images/mpl_09.png)

###### 图 7-9\. 具有两个不同缩放数据集的图表

检查 [图 7-9](#matplotlib_7) 发现，第一个数据集仍然“视觉可读”，而第二个数据集现在看起来像是直线，因为 y 轴的新缩放。在某种意义上，第二个数据集的信息现在“视觉上丢失了”。解决这个问题有两种基本方法：

+   使用两个 y 轴（左/右）

+   使用两个子图（上/下，左/右）

让我们先将第二个 y 轴引入图表中。[图 7-10](#matplotlib_8) 现在有了两个不同的 y 轴。左侧的 y 轴用于第一个数据集，而右侧的 y 轴用于第二个数据集。因此，也有了两个图例：

```py
In [21]: fig, ax1 = plt.subplots()  ![1](images/1.png)
         plt.plot(y[:, 0], 'b', lw=1.5, label='1st')
         plt.plot(y[:, 0], 'ro')
         plt.legend(loc=8)
         plt.xlabel('index')
         plt.ylabel('value 1st')
         plt.title('A Simple Plot')
         ax2 = ax1.twinx()  ![2](images/2.png)
         plt.plot(y[:, 1], 'g', lw=1.5, label='2nd')
         plt.plot(y[:, 1], 'ro')
         plt.legend(loc=0)
         plt.ylabel('value 2nd');
         # plt.savefig('../../images/ch07/mpl_10')
```

[![1](images/1.png)](#co_financial_data_science_CO7-1)

定义 `figure` 和 `axis` 对象。

[![2](images/2.png)](#co_financial_data_science_CO7-2)

创建共享 x 轴的第二个 `axis` 对象。

![mpl 10](images/mpl_10.png)

###### 图 7-10\. 具有两个数据集和两个 y 轴的图表

关键代码行是帮助管理坐标轴的代码行。这些是接下来的代码行：

```py
fig, ax1 = plt.subplots()
ax2 = ax1.twinx()
```

通过使用 `plt.subplots()` 函数，我们直接访问基础绘图对象（图形、子图等）。例如，它允许生成一个共享 x 轴的第二个子图。在[图 7-10](#matplotlib_8)中，我们实际上有两个子图*叠加*在一起。

接下来，考虑两个*分离*子图的情况。这个选项给予了更多处理两个数据集的自由，就像[图 7-11](#matplotlib_9)所示：

```py
In [22]: plt.figure(figsize=(10, 6))
         plt.subplot(211)  ![1](images/1.png)
         plt.plot(y[:, 0], lw=1.5, label='1st')
         plt.plot(y[:, 0], 'ro')
         plt.legend(loc=0)
         plt.ylabel('value')
         plt.title('A Simple Plot')
         plt.subplot(212)  ![2](images/2.png)
         plt.plot(y[:, 1], 'g', lw=1.5, label='2nd')
         plt.plot(y[:, 1], 'ro')
         plt.legend(loc=0)
         plt.xlabel('index')
         plt.ylabel('value');
         # plt.savefig('../../images/ch07/mpl_11')
```

[![1](images/1.png)](#co_financial_data_science_CO8-1)

定义了上方子图 1。

[![2](images/2.png)](#co_financial_data_science_CO8-2)

定义了下方子图 2。

![mpl 11](images/mpl_11.png)

###### 图 7-11\. 具有两个子图的绘图

`matplotlib` `figure` 对象中子图的放置是通过使用特殊的坐标系统来完成的。`plt.subplot()` 接受三个整数作为参数，分别为 `numrows`、`numcols` 和 `fignum`（用逗号分隔或不分隔）。`numrows` 指定*行*的数量，`numcols` 指定*列*的数量，而 `fignum` 指定*子图*的数量，从 1 开始，以 `numrows * numcols` 结束。例如，具有九个等大小子图的图形将具有 `numrows=3`，`numcols=3` 和 `fignum=1,2,...,9`。右下方的子图将具有以下“坐标”：`plt.subplot(3, 3, 9)`。

有时，选择两种不同的图表类型来可视化这样的数据可能是必要的或者是期望的。通过子图的方法，您可以自由组合 `matplotlib` 提供的任意类型的图表。[1](ch07.html#idm140277668085472) [图 7-12](#matplotlib_10) 结合了线条/点图和柱状图：

```py
In [23]: plt.figure(figsize=(10, 6))
         plt.subplot(121)
         plt.plot(y[:, 0], lw=1.5, label='1st')
         plt.plot(y[:, 0], 'ro')
         plt.legend(loc=0)
         plt.xlabel('index')
         plt.ylabel('value')
         plt.title('1st Data Set')
         plt.subplot(122)
         plt.bar(np.arange(len(y)), y[:, 1], width=0.5,
                 color='g', label='2nd')  ![1](images/1.png)
         plt.legend(loc=0)
         plt.xlabel('index')
         plt.title('2nd Data Set');
         # plt.savefig('../../images/ch07/mpl_12')
```

[![1](images/1.png)](#co_financial_data_science_CO9-1)

创建一个 `bar` 子图。

![mpl 12](images/mpl_12.png)

###### 图 7-12\. 将线条/点子图与柱状子图组合的绘图

## 其他绘图样式

在二维绘图方面，线条和点图可能是金融中最重要的图表之一；这是因为许多数据集包含时间序列数据，通常通过这些图表进行可视化。[第 8 章](ch08.html#fin_time_series)详细讨论了金融时间序列数据。然而，目前这一节还是采用了一个随机数的二维数据集，并且展示了一些备用的、对于金融应用有用的可视化方法。

第一种是*散点图*，其中一个数据集的值作为另一个数据集的``x``值。[图 7-13](#matplotlib_11_a) 展示了这样一个图。例如，此类图用于绘制一个金融时间序列的回报与另一个金融时间序列的回报。对于此示例，我们将使用一个新的二维数据集以及一些更多的数据：

```py
In [24]: y = np.random.standard_normal((1000, 2))  ![1](images/1.png)

In [25]: plt.figure(figsize=(10, 6))
         plt.plot(y[:, 0], y[:, 1], 'ro')  ![2](images/2.png)
         plt.xlabel('1st')
         plt.ylabel('2nd')
         plt.title('Scatter Plot');
         # plt.savefig('../../images/ch07/mpl_13')
```

[![1](images/1.png)](#co_financial_data_science_CO10-1)

创建一个包含随机数的较大数据集。

[![2](images/2.png)](#co_financial_data_science_CO10-2)

通过 `plt.plot()` 函数绘制散点图。

![mpl 13](images/mpl_13.png)

###### 图 7-13\. 通过 plot 函数绘制散点图

`matplotlib` 还提供了一个特定的函数来生成散点图。它基本上工作方式相同，但提供了一些额外的功能。[图 7-14](#matplotlib_11_b) 展示了使用 `plt.scatter()` 函数生成的相应散点图，这次与 [图 7-13](#matplotlib_11_a) 对应，:

```py
In [26]: plt.figure(figsize=(10, 6))
         plt.scatter(y[:, 0], y[:, 1], marker='o')  ![1](images/1.png)
         plt.xlabel('1st')
         plt.ylabel('2nd')
         plt.title('Scatter Plot');
         # plt.savefig('../../images/ch07/mpl_14')
```

[![1](images/1.png)](#co_financial_data_science_CO11-1)

通过 `plt.scatter()` 函数绘制的散点图。

![mpl 14](images/mpl_14.png)

###### 图 7-14\. 通过散点函数生成的散点图

例如，`plt.scatter()` 绘图函数允许添加第三个维度，可以通过不同的颜色来可视化，并且可以通过使用颜色条来描述。[图 7-15](#matplotlib_11_c) 展示了一个散点图，其中第三个维度通过单个点的不同颜色来说明，并且有一个颜色条作为颜色的图例。为此，以下代码生成了一个具有随机数据的第三个数据集，这次是介于 0 到 10 之间的整数：

```py
In [27]: c = np.random.randint(0, 10, len(y))

In [28]: plt.figure(figsize=(10, 6))
         plt.scatter(y[:, 0], y[:, 1],
                     c=c,  ![1](images/1.png)
                     cmap='coolwarm',  ![2](images/2.png)
                     marker='o')  ![3](images/3.png)
         plt.colorbar()
         plt.xlabel('1st')
         plt.ylabel('2nd')
         plt.title('Scatter Plot');
         # plt.savefig('../../images/ch07/mpl_15')
```

[![1](images/1.png)](#co_financial_data_science_CO12-1)

包含了第三个数据集。

[![2](images/2.png)](#co_financial_data_science_CO12-2)

选择了颜色映射。

[![3](images/3.png)](#co_financial_data_science_CO12-3)

将标记定义为粗点。

![mpl 15](images/mpl_15.png)

###### 图 7-15\. 具有第三维的散点图

另一种类型的图表，*直方图*，在金融收益的背景下也经常被使用。[图 7-16](#matplotlib_12_a) 将两个数据集的频率值放在同一个图表中相邻位置：

```py
In [29]: plt.figure(figsize=(10, 6))
         plt.hist(y, label=['1st', '2nd'], bins=25)  ![1](images/1.png)
         plt.legend(loc=0)
         plt.xlabel('value')
         plt.ylabel('frequency')
         plt.title('Histogram');
         # plt.savefig('../../images/ch07/mpl_16')
```

[![1](images/1.png)](#co_financial_data_science_CO13-1)

通过 `plt.hist()` 函数绘制直方图。

![mpl 16](images/mpl_16.png)

###### 图 7-16\. 两个数据集的直方图

由于直方图在金融应用中是如此重要的图表类型，让我们更近距离地看一下 `plt.hist` 的使用。以下示例说明了支持的参数：

```py
plt.hist(x, bins=10, range=None, normed=False, weights=None, cumulative=False, bottom=None, histtype='bar', align='mid', orientation='vertical', rwidth=None, log=False, color=None, label=None, stacked=False, hold=None, **kwargs)
```

[表 7-5](#hist_params) 提供了 `plt.hist` 函数的主要参数的描述。

表 7-5\. `plt.hist()` 的参数

| 参数 | 描述 |
| --- | --- |
| `x` | `list` 对象(s)，`ndarray` 对象 |
| `bins` | 柱子数量 |
| `range` | 柱的下限和上限 |
| `normed` | 规范化，使积分值为 1 |
| `weights` | `x` 中每个值的权重 |
| `cumulative` | 每个柱包含低位柱的计数 |
| `histtype` | 选项（字符串）：`bar`，`barstacked`，`step`，`stepfilled` |
| `align` | 选项（字符串）：`left`，`mid`，`right` |
| `orientation` | 选项（字符串）：`horizontal`，`vertical` |
| `rwidth` | 条柱的相对宽度 |
| `log` | 对数刻度 |
| `color` | 每个数据集的颜色（类似数组） |
| `label` | 用于标签的字符串或字符串序列 |
| `stacked` | 堆叠多个数据集 |

[图 7-17](#matplotlib_12_b) 展示了类似的图表；这次，两个数据集的数据在直方图中堆叠：

```py
In [30]: plt.figure(figsize=(10, 6))
         plt.hist(y, label=['1st', '2nd'], color=['b', 'g'],
                     stacked=True, bins=20, alpha=0.5)
         plt.legend(loc=0)
         plt.xlabel('value')
         plt.ylabel('frequency')
         plt.title('Histogram');
         # plt.savefig('../../images/ch07/mpl_17')
```

![mpl 17](images/mpl_17.png)

###### 图 7-17\. 两个数据集的堆叠直方图

另一种有用的绘图类型是*箱线图*。类似于直方图，箱线图既可以简明地概述数据集的特征，又可以轻松比较多个数据集。[图7-18](#matplotlib_13)展示了我们数据集的这样一个图：

```py
In [31]: fig, ax = plt.subplots(figsize=(10, 6))
         plt.boxplot(y)  ![1](images/1.png)
         plt.setp(ax, xticklabels=['1st', '2nd'])  ![2](images/2.png)
         plt.xlabel('data set')
         plt.ylabel('value')
         plt.title('Boxplot');
         # plt.savefig('../../images/ch07/mpl_18')
```

[![1](images/1.png)](#co_financial_data_science_CO14-1)

通过`plt.boxplot()`函数绘制箱线图。

[![2](images/2.png)](#co_financial_data_science_CO14-2)

设置各个x标签。

最后一个示例使用了函数`plt.setp()`，它为一个（组）绘图实例设置属性。例如，考虑由以下代码生成的线图：

```py
line = plt.plot(data, 'r')
```

下面的代码：

```py
plt.setp(line, linestyle='--')
```

将线条样式更改为“虚线”。这样，您可以在生成绘图实例（“艺术家对象”）之后轻松更改参数。

![mpl 18](images/mpl_18.png)

###### 图7-18\. 两个数据集的箱线图

作为本节的最后一个示例，请考虑一个在[matplotlib画廊](http://www.matplotlib.org/gallery.html)中也可以找到的受数学启发的绘图。它绘制了一个函数并在图形上突出显示了函数下方的区域，从下限到上限 — 换句话说，函数在下限和上限之间的积分值突出显示为一个区域。要说明的积分值是<math alttext="integral Subscript a Superscript b Baseline f left-parenthesis x right-parenthesis d x"><mrow><msubsup><mo>∫</mo> <mrow><mi>a</mi></mrow> <mi>b</mi></msubsup> <mi>f</mi> <mrow><mo>(</mo> <mi>x</mi> <mo>)</mo></mrow> <mi>d</mi> <mi>x</mi></mrow></math>，其中<math alttext="f left-parenthesis x right-parenthesis equals one-half dot e Superscript x Baseline plus 1"><mrow><mi>f</mi> <mrow><mo>(</mo> <mi>x</mi> <mo>)</mo></mrow> <mo>=</mo> <mfrac><mn>1</mn> <mn>2</mn></mfrac> <mo>·</mo> <msup><mi>e</mi> <mi>x</mi></msup> <mo>+</mo> <mn>1</mn></mrow></math>，<math alttext="a equals one-half"><mrow><mi>a</mi> <mo>=</mo> <mfrac><mn>1</mn> <mn>2</mn></mfrac></mrow></math>，<math><mrow><mi>b</mi> <mo>=</mo> <mfrac><mn>3</mn> <mn>2</mn></mfrac></mrow></math>。[图7-19](#matplotlib_math)显示了结果图，并演示了`matplotlib`如何无缝处理数学公式的`LaTeX`类型设置以将其包含到绘图中。首先，函数定义，积分限制作为变量以及x和y值的数据集。

```py
In [32]: def func(x):
             return 0.5 * np.exp(x) + 1  ![1](images/1.png)
         a, b = 0.5, 1.5  ![2](images/2.png)
         x = np.linspace(0, 2)  ![3](images/3.png)
         y = func(x)  ![4](images/4.png)
         Ix = np.linspace(a, b)  ![5](images/5.png)
         Iy = func(Ix) ![6](images/6.png)
         verts = [(a, 0)] + list(zip(Ix, Iy)) + [(b, 0)]  ![7](images/7.png)
```

[![1](images/1.png)](#co_financial_data_science_CO15-1)

函数定义。

[![2](images/2.png)](#co_financial_data_science_CO15-2)

积分限制。

[![3](images/3.png)](#co_financial_data_science_CO15-3)

用于绘制函数的x值。

[![4](images/4.png)](#co_financial_data_science_CO15-4)

用于绘制函数的y值。

[![5](images/5.png)](#co_financial_data_science_CO15-5)

积分限制内的x值。

[![6](images/6.png)](#co_financial_data_science_CO15-6)

积分限制内的y值。

[![7](images/7.png)](#co_financial_data_science_CO15-7)

包含多个表示要绘制的多边形的坐标的`list`对象。

其次，由于需要明确放置许多单个对象，绘图本身有点复杂。

```py
In [33]: from matplotlib.patches import Polygon
         fig, ax = plt.subplots(figsize=(10, 6))
         plt.plot(x, y, 'b', linewidth=2)  ![1](images/1.png)
         plt.ylim(ymin=0)  ![2](images/2.png)
         poly = Polygon(verts, facecolor='0.7', edgecolor='0.5')  ![3](images/3.png)
         ax.add_patch(poly)  ![3](images/3.png)
         plt.text(0.5 * (a + b), 1, r'$\int_a^b f(x)\mathrm{d}x$',
                  horizontalalignment='center', fontsize=20)  ![4](images/4.png)
         plt.figtext(0.9, 0.075, '$x$')  ![5](images/5.png)
         plt.figtext(0.075, 0.9, '$f(x)$')  ![5](images/5.png)
         ax.set_xticks((a, b))  ![6](images/6.png)
         ax.set_xticklabels(('$a$', '$b$'))  ![6](images/6.png)
         ax.set_yticks([func(a), func(b)])  ![7](images/7.png)
         ax.set_yticklabels(('$f(a)$', '$f(b)$'))  ![7](images/7.png)
         # plt.savefig('../../images/ch07/mpl_19')
Out[33]: [<matplotlib.text.Text at 0x1066af438>, <matplotlib.text.Text at 0x10669ba20>]
```

[![1](images/1.png)](#co_financial_data_science_CO16-1)

将函数值绘制为蓝线。

[![2](images/2.png)](#co_financial_data_science_CO16-2)

定义纵坐标轴的最小 y 值。

[![3](images/3.png)](#co_financial_data_science_CO16-3)

以灰色绘制多边形（积分区域）。

[![4](images/4.png)](#co_financial_data_science_CO16-5)

将积分公式放置在图中。

[![5](images/5.png)](#co_financial_data_science_CO16-6)

放置轴标签。

[![6](images/6.png)](#co_financial_data_science_CO16-8)

放置 x 标签。

[![7](images/7.png)](#co_financial_data_science_CO16-10)

放置 y 标签。

![mpl 19](images/mpl_19.png)

###### 图 7-19。指数函数、积分区域和 LaTeX 标签

# 静态 3D 绘图

在金融领域，确实没有太多领域真正受益于三维可视化。然而，一个应用领域是同时显示一系列到期时间和行权价的隐含波动率的波动率曲面。接下来，代码人工生成类似于波动率曲面的图形。为此，请考虑：

+   *50到150之间的行权价值*

+   *0.5到2.5年的到期时间*

这提供了一个二维坐标系。`NumPy`的`np.meshgrid()`函数可以从两个一维`ndarray`对象生成这样的系统：

```py
In [34]: strike = np.linspace(50, 150, 24)  ![1](images/1.png)

In [35]: ttm = np.linspace(0.5, 2.5, 24)  ![2](images/2.png)

In [36]: strike, ttm = np.meshgrid(strike, ttm)  ![3](images/3.png)

In [37]: strike[:2].round(1)  ![3](images/3.png)
Out[37]: array([[ 50. ,  54.3,  58.7,  63. ,  67.4,  71.7,  76.1,  80.4,  84.8,
                  89.1,  93.5,  97.8, 102.2, 106.5, 110.9, 115.2, 119.6, 123.9,
                 128.3, 132.6, 137. , 141.3, 145.7, 150. ],
                [ 50. ,  54.3,  58.7,  63. ,  67.4,  71.7,  76.1,  80.4,  84.8,
                  89.1,  93.5,  97.8, 102.2, 106.5, 110.9, 115.2, 119.6, 123.9,
                 128.3, 132.6, 137. , 141.3, 145.7, 150. ]])

In [38]: iv = (strike - 100) ** 2 / (100 * strike) / ttm  ![4](images/4.png)

In [39]: iv[:5, :3]  ![4](images/4.png)
Out[39]: array([[1.        , 0.76695652, 0.58132045],
                [0.85185185, 0.65333333, 0.4951989 ],
                [0.74193548, 0.56903226, 0.43130227],
                [0.65714286, 0.504     , 0.38201058],
                [0.58974359, 0.45230769, 0.34283001]])
```

[![1](images/1.png)](#co_financial_data_science_CO17-1)

`ndarray`对象中的行权价值。

[![2](images/2.png)](#co_financial_data_science_CO17-2)

`ndarray` 对象中的时间至到期值。

[![3](images/3.png)](#co_financial_data_science_CO17-3)

创建的两个二维`ndarray`对象（网格）。

[![4](images/4.png)](#co_financial_data_science_CO17-5)

虚拟的隐含波动率值。

由以下代码生成的图形显示在[图 7-20](#matplotlib_17)中：

```py
In [40]: from mpl_toolkits.mplot3d import Axes3D  ![1](images/1.png)
         fig = plt.figure(figsize=(10, 6))
         ax = fig.gca(projection='3d')  ![2](images/2.png)
         surf = ax.plot_surface(strike, ttm, iv, rstride=2, cstride=2,
                                cmap=plt.cm.coolwarm, linewidth=0.5,
                                antialiased=True)  ![3](images/3.png)
         ax.set_xlabel('strike')  ![4](images/4.png)
         ax.set_ylabel('time-to-maturity')  ![5](images/5.png)
         ax.set_zlabel('implied volatility')  ![6](images/6.png)
         fig.colorbar(surf, shrink=0.5, aspect=5);  ![7](images/7.png)
         # plt.savefig('../../images/ch07/mpl_20')
```

[![1](images/1.png)](#co_financial_data_science_CO18-1)

导入相关的 3D 绘图特性。

[![2](images/2.png)](#co_financial_data_science_CO18-2)

为 3D 绘图设置画布。

[![3](images/3.png)](#co_financial_data_science_CO18-3)

创建 3D 图。

[![4](images/4.png)](#co_financial_data_science_CO18-4)

设置 x 标签。

[![5](images/5.png)](#co_financial_data_science_CO18-5)

设置 y 标签。

[![6](images/6.png)](#co_financial_data_science_CO18-6)

设置 z 标签。

[![7](images/7.png)](#co_financial_data_science_CO18-7)

这将创建一个色标。

![mpl 20](images/mpl_20.png)

###### 图 7-20。 (虚拟) 隐含波动率的三维曲面图

[表 7-6](#plot_surface_params)提供了`plt.plot_surface()`函数可以接受的不同参数的描述。

表 7-6。`plot_surface`的参数

| 参数 | 描述 |
| --- | --- |
| `X, Y, Z` | 数据值为 2D 数组 |
| `rstride` | 数组行跨度（步长） |
| `cstride` | 数组列跨度（步长） |
| `color` | 表面补丁的颜色 |
| `cmap` | 表面补丁的颜色映射 |
| `facecolors` | 各个补丁的面颜色 |
| `norm` | 用于将值映射到颜色的 `Normalize` 的实例 |
| `vmin` | 要映射的最小值 |
| `vmax` | 要映射的最大值 |
| `shade` | 是否着色面颜色 |

与二维图相似，线型可以由单个点或如下所示的单个三角形替代。[图 7-21](#matplotlib_18) 将相同的数据绘制为 3D 散点图，但现在还使用 `view_init()` 方法设置了不同的视角：

```py
In [41]: fig = plt.figure(figsize=(10, 6))
         ax = fig.add_subplot(111, projection='3d')
         ax.view_init(30, 60)  ![1](images/1.png)
         ax.scatter(strike, ttm, iv, zdir='z', s=25,
                    c='b', marker='^')  ![2](images/2.png)
         ax.set_xlabel('strike')
         ax.set_ylabel('time-to-maturity')
         ax.set_zlabel('implied volatility');
         # plt.savefig('../../images/ch07/mpl_21')
```

[![1](images/1.png)](#co_financial_data_science_CO19-1)

设置视角。

[![2](images/2.png)](#co_financial_data_science_CO19-2)

创建 3D 散点图。

![mpl 21](images/mpl_21.png)

###### 图 7-21\. (虚拟的) 隐含波动率的 3D 散点图

# 交互式 2D 绘图

`matplotlib` 允许创建静态位图对象或 PDF 格式的绘图。如今，有许多可用于基于 `D3.js` 标准创建交互式绘图的库。这些绘图可以实现缩放和悬停效果以进行数据检查，还可以轻松嵌入到网页中。

一个流行的平台和绘图库是[`Plotly`](http://plot.ly)。它专门用于数据科学的可视化，并在全球范围内广泛使用。Plotly 的主要优点是其与 Python 生态系统的紧密集成以及易于使用 — 特别是与 `pandas` 的 `DataFrame` 对象和包装器包 [`Cufflinks`](http://github.com/santosjorge/cufflinks) 结合使用时。

对于某些功能，需要使用 Plotly 的免费帐户，用户可以在平台本身 [*http://plot.ly*](http://plot.ly) 下注册。一旦授予凭据，它们应该被本地存储以供以后永久使用。所有相关详细信息都在[使用 Plotly for Python 入门](https://plot.ly/python/getting-started/) 中找到。

本节仅关注选定的方面，因为 `Cufflinks` 专门用于从存储在 `DataFrame` 对象中的数据创建交互式绘图。

## 基本绘图

要从 Jupyter Notebook 上下文开始，需要一些导入，并且应该打开 *notebook 模式*。

```py
In [42]: import pandas as pd

In [43]: import cufflinks as cf  ![1](images/1.png)

In [44]: import plotly.offline as plyo  ![2](images/2.png)

In [45]: plyo.init_notebook_mode(connected=True)  ![3](images/3.png)
```

[![1](images/1.png)](#co_financial_data_science_CO20-1)

导入 `Cufflinks`。

[![2](images/2.png)](#co_financial_data_science_CO20-2)

导入 `Plotly` 的离线绘图功能。

[![3](images/3.png)](#co_financial_data_science_CO20-3)

打开笔记本绘图模式。

###### 提示

使用`Plotly`，还可以选择在`Plotly`服务器上呈现绘图。然而，笔记本模式通常更快，特别是处理较大数据集时。但是，像`Plotly`的流图服务之类的一些功能仅通过与服务器通信才可用。

后续示例再次依赖随机数，这次存储在具有`DatetimeIndex`的`DataFrame`对象中，即作为时间序列数据。

```py
In [46]: a = np.random.standard_normal((250, 5)).cumsum(axis=0)  ![1](images/1.png)

In [47]: index = pd.date_range('2019-1-1',  ![2](images/2.png)
                               freq='B',  ![3](images/3.png)
                               periods=len(a)) ![4](images/4.png)

In [48]: df = pd.DataFrame(100 + 5 * a,  ![5](images/5.png)
                           columns=list('abcde'),  ![6](images/6.png)
                           index=index)  ![7](images/7.png)

In [49]: df.head()  ![8](images/8.png)
Out[49]:                      a           b           c          d           e
         2019-01-01  109.037535   98.693865  104.474094  96.878857  100.621936
         2019-01-02  107.598242   97.005738  106.789189  97.966552  100.175313
         2019-01-03  101.639668  100.332253  103.183500  99.747869  107.902901
         2019-01-04   98.500363  101.208283  100.966242  94.023898  104.387256
         2019-01-07   93.941632  103.319168  105.674012  95.891062   86.547934
```

[![1](images/1.png)](#co_financial_data_science_CO21-1)

标准正态分布的（伪）随机数。

[![2](images/2.png)](#co_financial_data_science_CO21-2)

`DatetimeIndex`对象的开始日期。

[![3](images/3.png)](#co_financial_data_science_CO21-3)

频率（“`business daily`“）。

[![4](images/4.png)](#co_financial_data_science_CO21-4)

所需周期数。

[![5](images/5.png)](#co_financial_data_science_CO21-5)

原始数据进行线性转换。

[![6](images/6.png)](#co_financial_data_science_CO21-6)

将列标题作为单个字符。

[![7](images/7.png)](#co_financial_data_science_CO21-7)

`DatetimeIndex`对象。

[![8](images/8.png)](#co_financial_data_science_CO21-8)

前五行的数据。

`Cufflinks`为`DataFrame`类添加了一个新方法：`df.iplot()`。此方法在后台使用`Plotly`创建交互式图。本节中的代码示例都利用了将交互式图下载为静态位图的选项，然后将其嵌入到文本中。在Jupyter Notebook环境中，创建的绘图都是交互式的。下面代码的结果显示为<<>>。

```py
In [50]: plyo.iplot(  ![1](images/1.png)
             df.iplot(asFigure=True),  ![2](images/2.png)
             # image ='png', ![3](images/3.png)
             filename='ply_01'  ![4](images/4.png)
         )
```

[![1](images/1.png)](#co_financial_data_science_CO22-1)

这利用了`Plotly`的离线（笔记本模式）功能。

[![2](images/2.png)](#co_financial_data_science_CO22-2)

使用参数`asFigure=True`调用`df.iplot()`方法以允许本地绘图和嵌入。

[![3](images/3.png)](#co_financial_data_science_CO22-3)

`image`选项还提供了绘图的静态位图版本。

[![4](images/4.png)](#co_financial_data_science_CO22-4)

指定要保存的位图的文件名（文件类型扩展名会自动添加）。

![ply 01](images/ply_01.png)

###### 图 7-22\. 使用`Plotly`、`pandas`和`Cufflinks`绘制时间序列数据的线图

与`matplotlib`一般或`pandas`绘图功能一样，可用于自定义此类绘图的多个参数（参见[图 7-23](#plotly_02)）：

```py
In [51]: plyo.iplot(
             df[['a', 'b']].iplot(asFigure=True,
                      theme='polar',  ![1](images/1.png)
                      title='A Time Series Plot',  ![2](images/2.png)
                      xTitle='date',  ![3](images/3.png)
                      yTitle='value',  ![4](images/4.png)
                      mode={'a': 'markers', 'b': 'lines+markers'},  ![5](images/5.png)
                      symbol={'a': 'dot', 'b': 'diamond'},  ![6](images/6.png)
                      size=3.5,  ![7](images/7.png)
                      colors={'a': 'blue', 'b': 'magenta'},  ![8](images/8.png)
                                 ),
             # image ='png',
             filename='ply_02'
         )
```

[![1](images/1.png)](#co_financial_data_science_CO23-1)

选择绘图的主题（绘图样式）。

[![2](images/2.png)](#co_financial_data_science_CO23-2)

添加标题。

[![3](images/3.png)](#co_financial_data_science_CO23-3)

添加 x 标签。

[![4](images/4.png)](#co_financial_data_science_CO23-4)

添加 y 标签。

[![5](images/5.png)](#co_financial_data_science_CO23-5)

按列定义绘图*模式*（线条、标记等）。

[![6](images/6.png)](#co_financial_data_science_CO23-6)

按列定义要用作标记的符号。

[![7](images/7.png)](#co_financial_data_science_CO23-7)

为所有标记固定大小。

[![8](images/8.png)](#co_financial_data_science_CO23-8)

按列指定绘图颜色

![ply 02](images/ply_02.png)

###### 图 7-23\. `DataFrame` 对象的两列线图及自定义

与 `matplotlib` 类似，`Plotly` 允许使用多种不同的绘图类型。通过 `Cufflinks` 可用的绘图有：`chart, scatter, bar, box, spread, ratio, heatmap, surface, histogram, bubble, bubble3d, scatter3d, scattergeo, ohlc, candle, pie` 和 `choroplet`。作为与线图不同的绘图类型的示例，请考虑直方图（参见[链接即将到来]）：

```py
In [52]: plyo.iplot(
             df.iplot(kind='hist',  ![1](images/1.png)
                      subplots=True,  ![2](images/2.png)
                      bins=15,  ![3](images/3.png)
                      asFigure=True),
             # image ='png',
             filename='ply_03'
         )
```

[![1](images/1.png)](#co_financial_data_science_CO24-1)

指定绘图类型。

[![2](images/2.png)](#co_financial_data_science_CO24-2)

每列需要单独的子图。

[![3](images/3.png)](#co_financial_data_science_CO24-3)

设置 `bins` 参数（要使用的桶=要绘制的条形图）。

![ply 03](images/ply_03.png)

###### 图 7-24\. `DataFrame` 对象的每列直方图

## 金融图表

当处理金融时间序列数据时，`Ploty`、`Cufflinks` 和 `pandas` 的组合特别强大。`Cufflinks` 提供了专门的功能，用于创建典型的金融图，并添加典型的金融图表元素，例如相对强度指标（RSI），这只是一个例子。为此，创建了一个持久的 `QuantFig` 对象，可以像使用 `Cufflinks` 的 `DataFrame` 对象一样绘制。

此子部分使用真实的金融数据集：欧元/美元汇率的时间序列数据（来源：FXCM Forex Capital Markets Ltd.）。

```py
In [53]: # data from FXCM Forex Capital Markets Ltd.
         raw = pd.read_csv('../../source/fxcm_eur_usd_eod_data.csv',
                          index_col=0, parse_dates=True)  ![1](images/1.png)

In [54]: raw.info()  ![2](images/2.png)

         <class 'pandas.core.frame.DataFrame'>
         DatetimeIndex: 2820 entries, 2007-06-03 to 2017-05-31
         Data columns (total 10 columns):
         Time          2820 non-null object
         OpenBid       2820 non-null float64
         HighBid       2820 non-null float64
         LowBid        2820 non-null float64
         CloseBid      2820 non-null float64
         OpenAsk       2820 non-null float64
         HighAsk       2820 non-null float64
         LowAsk        2820 non-null float64
         CloseAsk      2820 non-null float64
         TotalTicks    2820 non-null int64
         dtypes: float64(8), int64(1), object(1)
         memory usage: 242.3+ KB

In [55]: quotes = raw[['OpenAsk', 'HighAsk', 'LowAsk', 'CloseAsk']]  ![3](images/3.png)
         quotes = quotes.iloc[-60:]  ![4](images/4.png)
         quotes.tail()  ![5](images/5.png)
Out[55]:             OpenAsk  HighAsk   LowAsk  CloseAsk
         Date
         2017-05-27  1.11808  1.11808  1.11743   1.11788
         2017-05-28  1.11788  1.11906  1.11626   1.11660
         2017-05-29  1.11660  1.12064  1.11100   1.11882
         2017-05-30  1.11882  1.12530  1.11651   1.12434
         2017-05-31  1.12434  1.12574  1.12027   1.12133
```

[![1](images/1.png)](#co_financial_data_science_CO25-1)

从逗号分隔值（CSV）文件中读取财务数据。

[![2](images/2.png)](#co_financial_data_science_CO25-2)

结果 `DataFrame` 对象包含多列和超过 2,800 行数据。

[![3](images/3.png)](#co_financial_data_science_CO25-3)

从 `DataFrame` 对象中选择四列（开-高-低-收）。

[![4](images/4.png)](#co_financial_data_science_CO25-4)

仅用于可视化的少量数据行。

[![5](images/5.png)](#co_financial_data_science_CO25-5)

结果数据集 `quotes` 的最后五行。

在实例化期间，`QuantFig` 对象将 `DataFrame` 对象作为输入，并允许进行一些基本的自定义。然后使用 `qf.iplot()` 方法绘制存储在 `QuantFig` 对象 `qf` 中的数据（参见[图 7-25](#qf_01)）。

```py
In [56]: qf = cf.QuantFig(
                  quotes,  ![1](images/1.png)
                  title='EUR/USD Exchange Rate',  ![2](images/2.png)
                  legend='top',  ![3](images/3.png)
                  name='EUR/USD'  ![4](images/4.png)
         )

In [57]: plyo.iplot(
             qf.iplot(asFigure=True),
             # image ='png',
             filename='qf_01'
         )
```

[![1](images/1.png)](#co_financial_data_science_CO26-1)

`DataFrame` 对象传递给 `QuantFig` 构造函数。

[![2](images/2.png)](#co_financial_data_science_CO26-2)

添加图标题。

[![3](images/3.png)](#co_financial_data_science_CO26-3)

图例放置在图的顶部。

[![4](images/4.png)](#co_financial_data_science_CO26-4)

这给数据集起了个名字。

![qf 01](images/qf_01.png)

###### 图 7-25\. EUR/USD 数据的 OHLC 图

添加典型的金融图表元素，如 Bollinger 带，通过 `QuantFig` 对象的不同可用方法进行 (见[图 7-26](#qf_02))。

```py
In [58]: qf.add_bollinger_bands(periods=15,  ![1](images/1.png)
                                boll_std=2)  ![2](images/2.png)

In [59]: plyo.iplot(qf.iplot(asFigure=True),
              # image='png',
              filename='qf_02')
```

[![1](images/1.png)](#co_financial_data_science_CO27-1)

Bollinger 带的周期数。

[![2](images/2.png)](#co_financial_data_science_CO27-2)

用于带宽的标准偏差数。

![qf 02](images/qf_02.png)

###### 图 7-26\. EUR/USD 数据的 OHLC 图，带有 Bollinger 带

添加了某些金融指标，如 RSI，作为一个子图 (见[图 7-27](#qf_03))。

```py
In [60]: qf.add_rsi(periods=14,  ![1](images/1.png)
                   showbands=False)  ![2](images/2.png)

In [61]: plyo.iplot(
              qf.iplot(asFigure=True),
              # image='png',
              filename='qf_03'
         )
```

[![1](images/1.png)](#co_financial_data_science_CO28-1)

修复了 RSI 周期。

[![2](images/2.png)](#co_financial_data_science_CO28-2)

不显示上限或下限带。

![qf 03](images/qf_03.png)

###### 图 7-27\. EUR/USD 数据的 OHLC 图，带有 Bollinger 带和 RSI

# 结论

当涉及到 Python 中的数据可视化时，`matplotlib` 可以被认为是基准和工作马。它与 `NumPy` 和 `pandas` 紧密集成。基本功能易于方便地访问。然而，另一方面，`matplotlib` 是一个相当强大的库，具有一种相对复杂的 API。这使得在本章中无法对 `matplotlib` 的所有功能进行更广泛的概述。

本章介绍了在许多金融背景下有用的 `matplotlib` 的 2D 和 3D 绘图的基本功能。其他章节提供了如何在可视化中使用这个基本库的更多示例。

除了 `matplotlib`，本章还涵盖了 `Plotly` 与 `Cufflinks` 的组合。这种组合使得创建交互式 `D3.js` 图表成为一件方便的事情，因为通常只需在 `DataFrame` 对象上进行一次方法调用。所有的技术细节都在后端处理。此外，`Cufflinks` 通过 `QuantFig` 对象提供了一种创建带有流行金融指标的典型金融图表的简单方法。

# 进一步阅读

`matplotlib` 的主要资源可以在网络上找到：

+   `matplotlib` 的主页，当然，是最好的起点：[*http://matplotlib.org*](http://matplotlib.org)。

+   有许多有用示例的画廊：[*http://matplotlib.org/gallery.html*](http://matplotlib.org/gallery.html)。

+   一个用于 2D 绘图的教程在这里：[*http://matplotlib.org/users/pyplot_tutorial.html*](http://matplotlib.org/users/pyplot_tutorial.html)。

+   另一个用于 3D 绘图的是：[*http://matplotlib.org/mpl_toolkits/mplot3d/tutorial.html*](http://matplotlib.org/mpl_toolkits/mplot3d/tutorial.html)。

现在已经成为一种标准的例程去参考画廊，寻找合适的可视化示例，并从相应的示例代码开始。

`Plotly` 和 `Cufflinks` 的主要资源也可以在线找到：

+   `matplotlib` 的主页：[*http://matplotlib.org*](http://matplotlib.org)

+   一个 Python 入门教程：[*https://plot.ly/python/getting-started/*](https://plot.ly/python/getting-started/)

+   `Cufflinks` 的 Github 页面：[*https://github.com/santosjorge/cufflinks*](https://github.com/santosjorge/cufflinks)

^([1](ch07.html#idm140277668085472-marker)) 想了解可用的绘图类型概述，请访问[`matplotlib` gallery](http://matplotlib.org/gallery.html)。
