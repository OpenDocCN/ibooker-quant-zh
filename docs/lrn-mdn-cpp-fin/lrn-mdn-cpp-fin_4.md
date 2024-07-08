# 第五章：线性代数

# 介绍

线性代数是计算金融的重要组成部分，因此对于金融 C++软件开发而言，它是一个必要且基本的组件。目前在标准库中存在的选项大多限于`valarray`容器，稍后将简要讨论。

由于 C++在上世纪 90 年代没有 Fortran 平台提供的便利的内置多维数组功能，那些转向 C++的量化程序员经常陷入不便之境，选项有限。这些选项包括从头开始构建这些功能，与数值 Fortran 库（如 BLAS 和 LAPACK）的接口斗争，或以某种方式说服管理层投资于第三方 C++商业库。

当时有时采用的人为 DIY 解决方案包括将矩阵表示为`vector`的`vector`，或将数据保存在二维动态 C 数组中。这两种方法都不太理想，前者繁琐且低效，后者则暴露软件于原始指针和动态内存管理相关的风险。标准库中似乎有用的一个特性是`std::valarray`，但也不是没有争议。它一直存活至今，提供了适合矩阵和向量数学的向量化操作和函数。它的优缺点将在稍后讨论。

多年来，情况有了显著改善，发布了多个开源线性代数库供 C++使用。在这些库中，两个在计算金融领域中获得了相当大批评群体的是[Eigen](https://eigen.tuxfamily.org)库 **{1}** 和[Armadillo](http://arma.sourceforge.net) **{2}**。在高性能计算（HPC）领域，第三个备受关注的选择是[Blaze](https://bitbucket.org/blaze-lib/blaze)库 **{3}**。早期的库[uBLAS](https://www.boost.org/doc/libs/1_81_0/libs/numeric/ublas/doc/index.html) **{4}** 也作为 Boost 库的一部分可用；但它不包括前述库中提供的矩阵分解和其他功能。

作为一个旁注，这些库各自都有开源 R 接口包 **{5}**。这些包使得依赖于其中一个或多个库的 C++代码可以集成到 R 包中，通常是为了提高运行时性能。

最近，NVIDIA 发布了作为其 HPC SDK 的一部分的[GPU 加速 C++线性代数库](https://developer.nvidia.com/gpu-accelerated-libraries#linear-algebra) **{6}**。

包括这里提到的内容在内，比较列表中涵盖了开源和商业 C++线性代数库，可在[Wikipedia](https://en.wikipedia.org/wiki/Comparison_of_linear_algebra_libraries)上找到**{7}**。本章后面将更详细地介绍 Eigen 库。

计划中的 C++23 和 C++26 的新功能似乎最终将为标准库提供既强大又得到长久支持的线性代数功能。这些新功能中的核心是为 C++23 计划的`std::mdspan`多维数组表示。C++26 应当随后更新，增加一个标准化接口以支持外部 BLAS 兼容库，以及其自身的线性代数工具集。

下面，我们首先将时光倒流，探讨`valarray`的方便数学特性，然后演示它如何作为矩阵的代理使用。随后，我们将深入介绍当前的 Eigen 库，并展示基本的矩阵操作，以及在金融建模中经常使用的矩阵分解。最后，还将简要介绍近期标准库发布的建议。

# `valarray`和矩阵操作

我们已经看到，作为工作马的 STL 容器`std::vector`是表示数学向量的一个选项。可以使用 STL 算法执行常见的向量算术，如内积。然而，“设计为一种用于保存值的通用机制...并且适应容器、迭代器和算法的架构” {2.5 Stroustrup, Tour 2E} **{8}**，作为其实现的一部分，常见的算术向量运算符如加法和乘法并未包含在其中。

一个独立于 STL 的标准库容器类，称为`valarray`，确实支持算术运算符，并提供“通常被认为是严肃数值工作所必需的优化” {ibid}。随着切片和步幅函数也伴随在`valarray`类旁，它也可以促进更高维度的数组表示，特别是矩阵。

虽然`valarray`具有这些非常有用的属性，似乎使其成为矩阵运算的明显选择，但它却饱受好评与差评并存。这一情况可以追溯到其最初的规范，由于当时是否要求一种新的技术——表达式模板（即将推出），可以显著优化性能而进行的辩论而从未完全完成。最终，这一要求未被强制执行。因此，“最初的实现速度较慢，因此用户不愿依赖它。”（来源：[链接](https://wg21.link/p1673) **{9}**）

但截至本文撰写时，两个主流的标准库发行版已经实现了 `valarray` 的表达式模板版本，即伴随着 gcc 和 Clang 编译器的那些版本。此外，[Intel oneAPI DPC++/C++ Compiler](https://www.intel.com/content/www/us/en/developer/articles/tool/oneapi-standalone-components.html#dpcpp-cpp) **{10}** 还配备了其自己的高性能 `valarray` 实现。作为一个附带说明，`begin` 和 `end` 函数的特化也作为 C++11 的增强功能包含在内。

故事的寓意似乎是：了解你打算使用的实现的能力。如果其性能适合你的需求，那么它可能是矩阵/向量操作以及常见数学函数的矢量化版本的非常便利选项。此外，检查 `valarray` 的属性可能为标准库计划中未来的线性代数增强提供一些背景，尽管在幕后的实现在某些情况下会有显著差异。

## 算术运算符和数学函数

`valarray` 容器支持按元素进行的标准算术运算符，以及标量乘法。

例如，向量和表达式 <math alttext="3 bold v 1 plus one-half bold v 2"><mrow><mn>3</mn> <msub><mi>𝐯</mi> <mn>1</mn></msub> <mo>+</mo> <mfrac><mn>1</mn> <mn>2</mn></mfrac> <msub><mi>𝐯</mi> <mn>2</mn></msub></mrow></math> 可以自然地转录为 C++ 中使用 `valarray` 对象的数学语句：

```
import <valarray>;
. . .

	std::valarray<double> v1{ 1.0, 2.0, 3.0,
				  1.5, 2.5 };

	std::valarray<double> v2{ 10.0, -20.0, 30.0,
				 -15.0, 25.0 };

	double vec_sum = 3.0 * v1 + 0.5 * v2;    // vec_sum is also a valarray <double>
```

结果是

```
8 -4 24 -3 20
```

*逐元素*乘法也是通过`*`运算符实现的：

```
double prod = v1 * v2;
```

这给了我们

```
10 -40 90 -22.5 62.5
```

向量 <math alttext="v 1"><msub><mi>v</mi> <mn>1</mn></msub></math> 和 <math alttext="v 2"><msub><mi>v</mi> <mn>2</mn></msub></math> 的点（或内）积可通过在 `valarray` 类的 `sum()` 成员函数上调用前述结果来轻松获得：

```
double dot_prod = prod.sum();  	// Result = 100
```

除了 `sum` 外，`valarray` 还有 `max` 和 `min` 函数，以及一个 `apply(.)` 成员函数，它类似于 `std::transform` 应用辅助函数：

```
double v1_max = v1.max();	// 3.0
double v1_min = v1.min();	// 1.0

// u and w are valarray<double> types
auto u = v1.apply([](double x) -> double {return x * x; });
// Result: 1, 4, 9, 2.25, 6.25

auto w = v1.apply([](double x) -> double {return std::sin(x) + std::cos(x);});
// Result: 1.38177 0.493151 -0.848872 1.06823 -0.202671
```

`cmath` 函数的子集被方便地定义用于 `valarray` 的矢量化操作。例如，以下操作将返回一个包含应用于 `v1` 和 `neg_val` 中每个元素的相应函数映像的 `valarray`。请注意，我们也可以像普通数值类型一样使用减法运算符对每个元素取反。

```
// The result in each is a valarray<double>
auto sine_v1 = std::sin(v1);
auto log_v1 = std::log(v1);
auto abs_v1 = std::abs(neg_val);
auto exp_v1 = std::exp(neg_val);
auto neg_v1 = - v1;
```

最后，截至 C++11，类似于为 STL 容器提供的 `begin` 和 `end` 函数的特化已经为 `valarray` 实现。一个简单的例子如下：

```
template<typename T>
void print(T t) { cout << t << " "; }

std::for_each(std::begin(w), std::end(w), print<double>);
```

鉴于在`valarray`上给定`apply(.)`成员函数和已有的内置向量化数学函数，与 STL 容器相比，在`valarray`的情况下，可能不经常需要使用 STL 算法`for_each`和`transform`。

## 作为矩阵代理的`valarray`

`valarray`提供了表示多维数组的功能。在我们的情况下，我们特别关注将二维数组表示为矩阵的代理。这可以通过`slice(.)`成员函数来实现，该函数可以提取对单个行或列的引用。

为了演示这一点，让我们首先通过定义别名来简化表示方式

```
using mtx_array = std::valarray<double>;
```

接下来，创建一个`valarray`对象`val`，并将代码格式化，使其看起来像一个 4 <math alttext="times"><mo>×</mo></math> 3 矩阵：

```
mtx_array val{ 1.0, 2.0, 3.0,
			   1.5, 2.5, 3.5,
			   7.0, 8.0, 9.0,
			   7.5, 8.5, 9.5};
```

可以使用为`valarray`定义的`std::slice`函数检索第一行，使用方括号操作符。

```
auto slice_row01 = val[std::slice(0, 3, 1)];
```

这句话的意思是：

+   转到`valarray`的第一个元素：索引 0，值为 1.0。

+   选择 3 个元素，从第一个开始

+   使用*步长*为 1，在这种情况下意味着选择连续的三个按行排列的元素

同样地，第二列可以使用步长为 3 来检索，列数为：

```
auto slice_col02 = val[std::slice(1, 4, 3)];		// The 2nd rowwise element has index 1
```

需要注意的是，`slice(.)`函数返回一个较轻的`slice_array`类型，作为所选元素的引用，而不是完整的`valarray`。然而，它并不提供访问单个元素或计算新行（例如矩阵乘积）所需的成员函数和运算符。如果我们想将这些函数应用于行或列数据，我们将需要构造相应的新`valarray`对象。这将在下一个示例中看到，即计算一个矩阵中一行与另一个矩阵中一列的点积，这在执行矩阵乘法时是必需的选项。

为了演示这一点，假设我们有一个 5 <math alttext="times"><mo>×</mo></math> 3 和一个 3 <math alttext="times"><mo>×</mo></math> 5 的矩阵，每个都表示为`valarray`。注意，我们还分别存储了每个的行数和列数。

```
mtx_array va01{ 1.0, 2.0, 3.0,
 	            1.5, 2.5, 3.5,
		        4.0, 5.0, 6.0,
		        4.5, 5.5, 6.5,
			    7.0, 8.0, 9.0 };

unsigned va01_rows{ 5 }, va01_cols{ 3 };

mtx_array va02{ 1.0, 2.0, 3.0, 4.0, 5.0,
			    1.5, 2.5, 3.5, 4.5, 5.5,
		  	    5.0, 6.0, 7.0, 8.0, 8.5 };

unsigned va02_rows{ 3 }, va02_cols{ 5 };
```

如果我们要应用矩阵乘法，需要计算第一个“矩阵”的每一行与第二个“矩阵”的每一列的点积。例如，为了获得第三行与第二列的点积，我们首先需要对每个进行切片：

```
auto slice_01_row_03 = va01[std::slice(9, va01_cols, 1)];
auto slice_02_col_02 = va02[std::slice(1, va02_rows, 5)];
```

然而，`slice_array`上既不定义逐元素乘法，也不定义`sum()`成员函数，因此我们需要构造相应的`valarray`对象：

```
mtx_array va01_row03{ slice_01_row_03 };
mtx_array va02_col02{ slice_02_col_02 };
```

然后按通常的方式计算点积：

```
double dot_prod = (va01_row03 * va02_col02).sum();
```

###### 注意

正如前面所述，`slice_array`充当`valarray`中块的引用。在`slice_array`上未定义操作和成员函数，如`\*`和`sum`，但是赋值运算符如`*=`和`+=`是定义过的。因此，对`slice_array`的修改，如下面的示例中所示，也会反映在`valarray`本身中。如果我们将`va01`的第一行作为一个切片：

```
auto slice_01_row_01 = va01[std::slice(0, va01_cols, 1)];
```

然后应用赋值运算符

```
slice_01_row_01[0] *= 10.0;
slice_01_row_01[1] += 1.0;
slice_01_row_01[2] -= 3.0;
```

然后`valarray`的内容将是

```
10	3	0
1.5	2.5	3.5
7	8	9
7.5	8.5	9.5
```

总之，`valarray`方便地提供了在整个数组上应用数学运算符和函数的能力，类似于 Fortran 90，以及更多专注于数学的语言如 R 和 Matlab。然而，请记住，性能很大程度上取决于您标准库分发中使用的实现。

有关`valarray`、其历史以及其优缺点的更多信息，请参阅[《C++标准库 第二版》附带的在线补充章节](http://www.cppstdlib.com/cppstdlib_supplementary.pdf))。**{11}**

在`valarray`和 C++98 之后，C++在线性代数方面有了一些非常积极的发展，其中一些现在将被介绍。

# Eigen

Eigen 库的第一个版本于 2006 年发布。自那时起，截至 2021 年 8 月，它已扩展到版本 3.4.0。从版本 3.3.1 开始，它已根据合理宽松的 Mozilla Public License (MPL) 2.0 许可发布。

Eigen 由模板代码组成，使其非常容易包含到其他 C++项目中，在其标准安装中不需要链接到外部二进制文件。它的表达式模板的整合，促进了惰性评估，提供了增强的计算性能。在被选择用于著名的[TensorFlow](https://www.tensorflow.org)机器学习库以及[Stan 数学库](https://mc-stan.org/users/interfaces/math)合并之后，它的受欢迎程度进一步提升。**{12}** 关于它在金融领域适用性和受欢迎程度的更多背景可以在最近的[Quantstart](https://www.quantstart.com/articles/Eigen-Library-for-Matrix-Algebra-in-C/)文章中找到。**{13}**

最后，Eigen 库有非常好的文档，配有教程和示例，可以帮助新手快速上手。

## 惰性评估

惰性评估推迟和最小化了矩阵和向量运算中所需的操作数。C++中的表达式模板用于封装算术操作（即表达式）在模板内，延迟直到实际需要它们。这可以减少使用传统方法时创建的总操作数、赋值和临时对象的数量。

下面是基于更全面和图解讨论的延迟评估示例，可以在[彼得·戈特林的现代科学程序设计中](https://www.pearson.com/en-us/subject-catalog/p/discovering-modern-c-/P200000000286/9780136677642)找到更详细的信息。 **{15}**

假设您在数学意义上有四个向量，每个向量都有相同数量的固定元素，比如

<math alttext="bold v 1 comma bold v 2 comma bold v 3 comma bold v 4" display="block"><mrow><msub><mi>𝐯</mi> <mn>1</mn></msub> <mo>,</mo> <msub><mi>𝐯</mi> <mn>2</mn></msub> <mo>,</mo> <msub><mi>𝐯</mi> <mn>3</mn></msub> <mo>,</mo> <msub><mi>𝐯</mi> <mn>4</mn></msub></mrow></math>

并且您希望将它们的和存储在向量*y*中。传统方法是定义加法运算符，连续求和并将它们存储在临时对象中，最终计算最终和并分配给*y*。

在代码中，操作符可以以通用方式定义，使得向量加法对于任何算术类型都定义明确：

```
template <typename T>
std::vector<T> operator + (const std::vector<T>& a,
	const std::vector<T>& b)
{
	std::vector<T> res(a.size());
	for (size_t i = 0; i < a.size(); ++i)
		res[i] = a[i] + b[i];
	return res;
}
```

计算四个向量的和如下

```
	vector<double> v1{ 1.0, 2.0, 3.0 };
	vector<double> v2{ 1.5, 2.5, 3.5 };
	vector<double> v3{ 4.0, 5.0, 6.0 };
	vector<double> v4{ 4.5, 5.5, 6.5 };

	auto y = v1 + v2 + v3 + v4;
```

导致以下结果：

+   4 - 1 = 3 个`vector`实例创建（两个临时加一个最终的`y`实例）

+   (4 - 1) <math alttext="times"><mo>×</mo></math> 3 个`double`变量的分配

当向量数量（比如*m*）和每个向量中的元素数量（比如*n*）增加时，这将变得更加普遍：

+   m – 1 个堆上分配的`vector`对象：m - 2 个临时对象，加上一个返回对象(`y`)

+   <math alttext="left-parenthesis m minus 1 right-parenthesis n"><mrow><mo>(</mo> <mi>m</mi> <mo>-</mo> <mn>1</mn> <mo>)</mo> <mi>n</mi></mrow></math> 分配

使用延迟评估，我们可以减少总步骤数，从而提高“大”*m*和*n*的效率。更具体地说，这可以通过延迟添加直到所有数据准备好，然后仅在那时为结果中的每个元素执行求和来实现。在代码中，这可以通过编写如下函数来实现。

```
template <typename T>
std::vector<T> sum_four_vectors(const std::vector<T>& a, const std::vector<T>& b,
	const std::vector<T>& c, const std::vector<T>& d)
{
	// Assume a, b, c, and d all contain the same
	// number of elements:
	std::vector<T> sum(a.size());

	for (size_t i = 0; i < a.size(); ++i)
	{
		sum[i] = a[i] + b[i] + c[i] + d[i];
	}

	return sum;
}
```

现在，在这种情况下，

+   没有临时`vector`对象；只需要`sum`结果

+   减少分配数量至 *n* = 4

Eigen 文档在[延迟评估和别名](https://eigen.tuxfamily.org/dox/TopicLazyEvaluation.html)部分提供了更多背景信息。

上述示例演示了延迟评估如何工作，但显而易见的问题是为所有可能的固定向量数编写单独的求和函数是不现实的。使用表达式模板进行泛化是一个更具挑战性的问题，这里不包括详细介绍，但可以在戈特林的书中找到更多信息{ibid 12}，以及在[Vandevoorde、Josuttis 和 Gregor 的 C++模板综合书籍](http://tmplbook.com/)的第二十七章中 **{16}**。

与任何其他优化工具一样，不应盲目应用以为它会自动使您的代码更高效，因为在某些情况下，性能实际上可能会降低。

最后，对于金融风险管理中表达式模板的非常有趣的实际案例演示，建议观看由 Bowie Owens 在 CppCon 2019 上展示的主题演讲 [presented by Bowie Owens at CppCon 2019](https://www.youtube.com/watch?v=4IUCBx5fIv0) **{17}** 。

## Eigen 矩阵和向量

Eigen 库的核心不出意外地是`Matrix`模板类。它在`Eigen`命名空间中定义，并需要包含`Dense`头文件。在撰写本文时，对应的模块导入尚未标准化。这意味着头文件需要包含在模块的全局片段中。

`Matrix`类具有六个模板参数，但提供了多种别名作为特定类型。这些包括固定方阵维度最多为四的类型，以及用于任意行和列数的动态类型。`Matrix`所持有的数值类型也是一个模板参数，但此设置也已合并到各个别名中。例如，以下代码将构造并显示一个`double`值的固定 3 <math alttext="times"><mo>×</mo></math> 3 矩阵，以及一个`int`的 4 <math alttext="times"><mo>×</mo></math> 4 矩阵。在构造时可以使用花括号（统一）初始化按行加载数据。

```
#include <Eigen/Dense>
. . .

Eigen::Matrix3d dbl_mtx			// Contains 'double' elements
{
	{10.6, 41.2, 2.16},
	{41.9, 5.31, 13.68},
	{22.47, 57.43, 8.82}
};

Eigen::Matrix4i int_mtx			// Contains 'int' elements
{
	{24, 0, 23, 13},
	{8, 75, 0, 98},
	{11, 60, 1, 3 },
	{422, 55, 11, 55}
};

cout << dbl_mtx << endl << endl;
cout << int_mtx << endl << endl;
```

还要注意`<<`流操作符被重载，因此可以轻松地将结果显示在屏幕上（按行主序）。

```
 10.6  87.4 58.63
 41.9  53.1 13.68
22.47 57.43  88.2

 24   0  23  13
  8  75   0  98
 11  60   1   3
422  55  11  55
```

可以访问单独的行和列，使用基于 0 的索引。例如，第一个矩阵的第一列和第二个矩阵的第三列可以分别通过相应的访问器函数获取：

```
cout << dbl_mtx.col(0) << endl << endl;
cout << int_mtx.row(2) << endl << endl;
```

这导致以下屏幕输出：

```
10.6
41.9
22.47

11 60  1  3
```

从技术上讲，由`row`或`col`访问器返回的类型是`Eigen::Block`。它类似于从`valarray`访问的`slice_array`，因为它作为对数据的轻量级引用。与`slice_array`不同，它不包含任何数学运算符，如`+=`。

对于本书中考虑的大多数金融示例，矩阵的维度事先不会知道，也不一定是方阵。此外，内容通常是实数。因此，我们将主要关注`double`类型的 Eigen 动态形式，别名为`Eigen::MatrixXd`。

###### 注意

1.  正如刚才提到的，我们主要使用动态的 Eigen `MatrixXd`矩阵形式（其中`d`表示`double`数值元素）；然而，成员函数和非成员函数通常适用于任何从`Matrix`模板类派生的类。讨论这些函数时，它们与`Matrix`而不是`MatrixXd`的关系也可能被提及。类似地，Eigen 中的向量表示将使用`VectorXd`。

1.  线性代数必然涉及下标和上标，例如 <math alttext="x Subscript i j"><msub><mi>x</mi> <mrow><mi>i</mi><mi>j</mi></mrow></msub></math> ，在数学符号中，*i* 可能从 1 到 *m*，*j* 从 1 到 *n*。然而，C++ 中是从 0 开始索引的，因此数学语句中的 *i* = 1 将在 C++ 中表示为 `i = 0`，*j* = *n* 将表示为 `j = n - 1`，依此类推。

构造 `MatrixXd` 可以采用多种形式。数据可以按行主序输入，每行的初始化统一决定了行数和列数。或者，可以使用构造函数参数作为维度，通过按行主序流式输入数据。还有一种方法是逐个设置每个元素。这里展示了每种方法的示例：

```
using Eigen::MatrixXd;
. . .

MatrixXd mtx0
{
	{1.0, 2.0, 3.0},
	{4.0, 5.0, 6.0},
	{7.0, 8.0, 9.0},
	{10.0, 11.0, 12.0}
};

MatrixXd mtx1{4, 3};		// 4 rows, 3 columns
mtx1 << 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0, 10.0, 11.0, 12.0;

MatrixXd mtx3{2, 2};
mtx3(0, 0) = 3.0;
mtx3(1, 0) = 2.5;
mtx3(0, 1) = -1.0;
mtx3(1, 1) = mtx3(1, 0) + mtx3(0, 1);
```

请注意，圆括号运算符同时作为改变器和访问器，如第三个示例所示。

两个特殊情况，其中列数或行数为一，别名为 `VectorXd` 和 `RowVectorXd`。构造选项与上述 `MatrixXd` 的示例类似，如同在 Eigen 文档中展示的那样：

```
using Eigen::VectorXd;
using Eigen::RowVectorXd;
. . .

VectorXd a 	{ {1.5, 2.5, 3.5} };             // A column-vector with 3 coefficients
RowVectorXd b { {1.0, 2.0, 3.0, 4.0} };      // A row-vector with 4 coefficients

Eigen::VectorXd v(2);
v(0) = 4.0;
v(1) = v(0) - 1.0;
```

## 矩阵和向量数学运算

矩阵加法和减法都方便地通过`+`和`-`运算符的重载实现，类似于`valarray`。同样，这些也适用于向量。但与`valarray`不同的是，乘法运算符`*`表示矩阵乘法而不是逐元素乘积。针对广泛的逐元素操作，提供了单独的函数集。

要将两个矩阵 `A` 和 `B` 相乘，代码遵循自然的数学顺序：

```
MatrixXd A
{
	{1.0, 2.0, 3.0},
	{1.5, 2.5, 3.5},
	{4.0, 5.0, 6.0},
	{4.5, 5.5, 6.5},
	{7.0, 8.0, 9.0}
};

MatrixXd B
{
	{1.0, 2.0, 3.0, 4.0, 5.0},
	{1.5, 2.5, 3.5, 4.5, 5.5},
	{5.0, 6.0, 7.0, 8.0, 8.5}
};

MatrixXd prod_ab = A * B;
```

这使我们的输出结果为：

```
   19    25    31    37  41.5
22.75 30.25 37.75 45.25    51
 41.5  56.5  71.5  86.5  98.5
45.25 61.75 78.25 94.75   108
   64    88   112   136 155.5
```

###### 注意

在 Eigen 文档中，强烈建议“不要在 Eigen 的表达式中使用 `auto` 关键字，除非你对自己所做的事情非常确定。特别是，不要将 `auto` 关键字用作 `Matrix<>` 类型的替代品。”

其背后的原因需要进行关于与懒惰求值相关的模板的高级讨论。懒惰求值在效率上提供了优势，但它也可能涉及返回类型的使用 `auto`，可能是引用而不是完整的 `Matrix` 类型。这可能导致意外或未定义的行为。随着您对各种 Eigen 类型的了解越来越深入，这个问题会变得不那么重要，但在这个入门演示中，我们大多数时候会遵循这个警告。

更多信息请参阅[文档](https://eigen.tuxfamily.org/dox/TopicPitfalls.html)。**{18}**

`*` 运算符还被重载用于矩阵-向量和行向量-矩阵乘法。例如，假设我们有一个包含三种基金的投资组合，具有收益相关性矩阵和给定的个别基金波动率向量（年化）。作为一个典型问题，我们可能需要以这种形式构造协方差矩阵，以计算投资组合波动率。首先，为了形成协方差矩阵，我们将通过包含基金波动率的对角矩阵对相关性矩阵进行前置和后置乘法：

```
MatrixXd corr_mtx
{
	{1.0, 0.5, 0.25},
	{0.5, 1.0, -0.7},
	{0.25, -0.7, 1.0}
};

VectorXd vols{ {0.2, 0.1, 0.4 } };

MatrixXd cov_mtx = vols.asDiagonal() * corr_mtx * vols.asDiagonal();
```

注意 `VectorXd` 的成员函数 `asDiagonal()` 如何方便地形成一个以向量元素为对角线的对角矩阵。

然后，给定一个资金权重向量 <math alttext="omega"><mi>ω</mi></math> 总和为 1，投资组合波动率则是二次形式的平方根

<math alttext="omega Superscript sans-serif upper T Baseline bold upper Sigma omega" display="block"><mrow><msup><mi>ω</mi> <mi>𝖳</mi></msup> <mi>Σ</mi> <mi>ω</mi></mrow></math>

其中 <math alttext="bold upper Sigma"><mi>Σ</mi></math> 是协方差矩阵：

```
VectorXd fund_weights{ {0.6, -0.3, 0.7 } };
double port_vol = std::sqrt(fund_weights.transpose() * cov_mtx * fund_weights);
```

对于逐元素矩阵乘法，使用 `cwiseProduct` 成员函数。例如，要对维度相同的矩阵 <math alttext="bold upper A"><mi>𝐀</mi></math> 和 <math alttext="bold upper B Superscript sans-serif upper T"><msup><mi>𝐁</mi> <mi>𝖳</mi></msup></math> 中的个别元素进行乘法运算，我们可以这样写：

```
MatrixXd cwise_prod = A.cwiseProduct(B.transpose());
```

实际上，Eigen 的 `Matrix` 上有一组 `cwise...`（意思是逐系数）成员函数，可以在两个兼容矩阵上执行逐元素操作，如 `cwiseQuotient` 和 `cwiseNotEqual`。还有一元的 `cwise` 成员函数，返回每个元素的绝对值和平方根。这些可以在[Eigen 文档](https://eigen.tuxfamily.org/dox/group__QuickRefPage.html#title6)中找到。**{19}**

`*` 运算符应用于两个向量时，取决于哪个向量被转置。对于两个向量 u 和 v，点（内）积计算为

<math alttext="bold u Superscript sans-serif upper T Baseline bold v" display="block"><mrow><msup><mi>𝐮</mi> <mi>𝖳</mi></msup> <mi>𝐯</mi></mrow></math>

当应用转置于 v 时，外积的结果是：

<math display="block"><msup><mstyle style="font-weight:bold"><mi>u</mi> <mi>v</mi></mstyle> <mi>T</mi></msup></math>

因此，在使用向量和 `*` 运算符时需要小心。假设我们有：

```
VectorXd u{ {1.0, 2.0, 3.0} };
VectorXd v{ {0.5, -0.5, 1.0} };
```

下面的向量乘法将得到不同的结果：

```
double dp = u.transpose() * v;			// Returns 'double'
MatrixXd op = u * v.transpose();		// Returns a Matrix
```

第一个将产生一个实值为 2.5，而第二个将给出一个 3 <math alttext="times"><mo>×</mo></math> 3 的矩阵：

```
  0.5 -0.5   1
   1   -1    2
 1.5 -1.5    3
```

为了简化操作，Eigen 在 `VectorXd` 类上提供了一个成员函数 `dot`。通过以下方式编写

```
dp = u.dot(v);
```

或许应该更清楚地表明我们想要哪种乘积。结果将与以前相同，此操作也是可交换的。

## STL 兼容性

Eigen `Vector` 和 `Matrix` 类的一个非常好的特性是它们与标准模板库的兼容性。这意味着你可以遍历 Eigen 容器，应用 STL 算法，并与 STL 容器交换数据。

### STL 和 `VectorXd`

作为第一个例子，假设您希望从 t 分布中生成 12 个随机变量，并将结果放入`VectorXd`容器中。该过程本质上与我们看到的使用`std::vector`并应用带有 lambda 辅助函数的`std::generate`算法相同：

```
VectorXd u(12);							// 12 elements
std::mt19937_64 mt(100);				// Mersenne Twister engine, seed = 100
std::student_t_distribution<> tdist(5);	// 5 degrees of freedom
std::generate(u.begin(), u.end(), [&mt, &tdist]() {return tdist(mt); });
```

###### 注意

在最近的 Eigen 3.4 版本发布之前，`begin`和`end`成员函数未定义。在这种情况下，您需要使用`data`和`size`函数，如下所示：

```
std::generate(u.data()), u.data() + u.size(),
[&mt, &tdist]() {return tdist(mt); });
```

非修改算法（如`std::max_element`）也是有效的：

```
auto max_u = std::max_element(u.begin(), u.end());		// Returns iterator
```

数值算法，例如`std::inner_product`，也可以应用：

```
double dot_prod = std::inner_product(u.begin(), u.end(), v.begin(), 0.0);
```

`VectorXd`上也支持更干净的 C++20 范围版本：

```
VectorXd w(v.size());
std::ranges::transform(u, v, w.begin(), std::plus{});
```

### 从 STL 容器数据构造矩阵

矩阵数据也可以从 STL 容器中获取。这很方便，因为数据通常可以通过从其他未包含 Eigen 的源接口接收。关键是使用`Eigen::Map`，它设置对（视图的）`vector`数据的引用，而不是复制它。

作为第一个例子，存储在`std::vector`容器中的数据可以转移到`Eigen::Map`，然后可以用作`MatrixXd`的构造函数参数。注意，`Map`在其构造函数中接受指向`vector`第一个元素的指针（使用`data`成员函数），以及行数和列数。

```
std::vector<double> v{ 1.0, 2.0, 3.0, 4.0, 5.0, 6.0,
	7.0, 8.0, 9.0, 10.0, 11.0, 12.0 };

Eigen::Map<MatrixXd> mtx_map(v.data(), 4, 3);
```

默认情况下，`mtx_map`将提供对数据的行/列访问。请注意，与以前使用数据初始化列表创建`MatrixXd`不同，顺序将是列主序而不是行主序。使用`cout`输出如下：

```
1  5  9
2  6  10
3  7  11
4  8  12
```

由于`Map`是对`v`中数据的轻量级视图，它不像以前用数据初始化列表创建`MatrixXd`对象那样具有所有功能。在某种意义上类似于从`valarray`中取出的切片。如果您需要此功能，则可以通过将`Map`对象放入其构造函数中来构造`MatrixXd`实例：

```
MatrixXd mtx_from_std_vector{ mtx_map };
```

`Map`中数据的默认排列顺序将是*列主序*，这与早期使用数值数据构造的`MatrixXd`示例不同。如果需要行主序，可以在一开始指定行主序，但这将需要将`Eigen::Matrix`模板参数明确设置为`RowMajor`，因为`MatrixXd`没有自己的存储方法模板参数：

```
Eigen::Map<Eigen::Matrix<double, 4, 3, Eigen::RowMajor>>
    mtx_row_major_map{ v.data(), 4, 3 };
```

如果矩阵是方阵，您可以直接就地转置`Map`以将其放置在行主序中：

```
// Square matrix, place in row-major order:
std::vector<double> sq_data{ 1.0, 2.0, 3.0, 4.0, 5.0, 6.0,
	7.0, 8.0, 9.0 };

Eigen::Map<MatrixXd> sq_mtx_map{ sq_data.data(), 3, 3 };
sq_mtx_map.transposeInPlace();
```

结果如下：

```
1 2 3
4 5 6
7 8 9
```

###### 警告

尝试使用`Map`转置非方阵可能导致程序崩溃。在这种情况下，您需要在应用`transposeInPlace`之前创建一个完整的`MatrixXd`对象。

### 将 STL 算法应用于矩阵

STL 算法也可以按行或按列应用于矩阵。假设我们有一个 4 <math alttext="times"><mo>×</mo></math> 3 矩阵如下所示：

```
	MatrixXd vals
	{
		{ 9.0, 8.0, 7.0 },
		{ 3.0, 2.0, 1.0 },
		{ 9.5, 8.5, 7.5 },
		{ 3.5, 2.5, 1.5 }
	};
```

Eigen 中的`rowwise()`成员函数在`Matrix`上设置按行迭代。每个`row`是对相应数据的引用（视图），因此我们可以按如下方式就地对矩阵的每个元素进行平方：

```
for (auto row : vals.rowwise())
{
	std::ranges::transform(row, row.begin(), [](double x) {return x * x; });
}
```

`colwise()`成员函数类似，在这种情况下，对矩阵的每一列进行排序：

```
for (auto col : vals.colwise())
{
	std::ranges::sort(col);
}
```

应用这两种算法后的最终结果是

```
    9     4     1
12.25  6.25  2.25
   81    64    49
90.25 72.25 56.25
```

每个元素已经被平方，并且每列已按升序重新排列。

金融程序员经常需要编写代码来计算一组股票或基金价格的对数收益率。例如，假设我们有三个 ETF 的 11 个月价格，分别放在以下三列中：

```
25.5  8.0 70.5
31.0  7.5 71.0
29.5  8.5 77.5
33.5  5.5 71.5
26.5  9.5 72.5
34.5  8.5 75.5
28.5  9.0 72.0
23.5  7.5 73.5
28.0  8.0 72.5
31.5  9.0 73.0
32.5  9.5 74.5
```

计算对数收益率的第一步是计算每个价格的自然对数。可以通过逐行应用`transform`算法来完成：

```
for (auto row : prices_to_returns.rowwise())
{
	std::ranges::transform(row, row.begin(), [](double x) {return std::log(x); });
}
```

然后，要获得对数收益率，我们需要从每个对数价格中减去其前任。为此，我们可以对每列应用`adjacent_difference`数值算法：

```
for (auto col : prices_to_returns.colwise())
{
	std::adjacent_difference(col.begin(), col.end(), col.begin());
}
```

此结果仍然是一个 11 <math alttext="times"><mo>×</mo></math> 3 矩阵，第一行仍包含第一行中价格的对数。

```
   3.23868    2.07944    4.25561
  0.195309 -0.0645385 0.00706717
-0.0495969   0.125163  0.0875981
  0.127155  -0.435318 -0.0805805
 -0.234401   0.546544  0.0138891
  0.263815  -0.111226  0.0405461
 -0.191055  0.0571584 -0.0474665
 -0.192904  -0.182322  0.0206193
  0.175204  0.0645385 -0.0136988
  0.117783   0.117783 0.00687288
 0.0312525  0.0540672  0.0203397
```

我们需要的是仅月度回报，因此需要移除第一行。这可以通过应用 Eigen 3.4 中引入的`seq`函数来实现，它提供了从`Matrix`对象中提取子矩阵视图（`Eigen::Block`）的直观方式。这里的示例展示了如何提取第一行以下的所有行：

```
MatrixXd returns_mtx{ prices_to_returns(Eigen::seq(1, Eigen::last),
	Eigen::seq(0, Eigen::last)) };
```

这句话的意思是：

1.  从第二行（索引为 1）开始，包括到最后一行的所有行：`Eigen::seq(1, Eigen::last)`

1.  从第一个（索引为 0）到最后一个列获取所有列：`Eigen::seq(0, Eigen::last)`

1.  仅使用构造返回的`returns_mtx`中的此子矩阵数据。

`returns_mtx`中保存的结果仅为对数收益率：

```
  0.195309 -0.0645385 0.00706717
-0.0495969   0.125163  0.0875981
  0.127155  -0.435318 -0.0805805
 -0.234401   0.546544  0.0138891
  0.263815  -0.111226  0.0405461
 -0.191055  0.0571584 -0.0474665
 -0.192904  -0.182322  0.0206193
  0.175204  0.0645385 -0.0136988
  0.117783   0.117783 0.00687288
 0.0312525  0.0540672  0.0203397
```

现在，假设投资组合分配分别固定在 35%、40%和 25%（按列）。我们可以通过将分配向量乘以`returns_mtx`来获得每月的投资组合回报：

```
VectorXd monthly_returns = returns_mtx * allocations;
```

结果是

```
 0.0443094
 0.0546058
 -0.149768
   0.14005
 0.0579814
-0.0558726
  -0.13529
 0.0837121
 0.0900555
 0.0376502
```

## 矩阵分解与应用

矩阵分解在各种金融工程问题中当然至关重要。接下来，我们将讨论几个示例。

### 线性方程组和 LU 分解

在金融和经济中，线性方程组是无处不在的，特别是在优化、套期保值和预测问题中。为了展示如何在 Eigen 中找到解决方案，让我们看一个通用问题并在代码中实现它。LU（下/上三角矩阵）分解是数值方法中的常见方法。与自己编写代码相反，Eigen 可以在两行代码中完成工作。

假设我们想要解决以下线性方程组，求解<math alttext="x 1"><msub><mi>x</mi> <mn>1</mn></msub></math>，<math alttext="x 2"><msub><mi>x</mi> <mn>2</mn></msub></math>和<math alttext="x 3"><msub><mi>x</mi> <mn>3</mn></msub></math>：

<math alttext="3 x 1 minus 5 x 2 plus x 3 equals 0" display="block"><mrow><mn>3</mn> <msub><mi>x</mi> <mn>1</mn></msub> <mo>-</mo> <mn>5</mn> <msub><mi>x</mi> <mn>2</mn></msub> <mo>+</mo> <msub><mi>x</mi> <mn>3</mn></msub> <mo>=</mo> <mn>0</mn></mrow></math><math alttext="minus x 1 minus x 2 plus x 3 equals negative 4" display="block"><mrow><mo>-</mo> <msub><mi>x</mi> <mn>1</mn></msub> <mo>-</mo> <msub><mi>x</mi> <mn>2</mn></msub> <mo>+</mo> <msub><mi>x</mi> <mn>3</mn></msub> <mo>=</mo> <mo>-</mo> <mn>4</mn></mrow></math><math alttext="2 x 1 minus 4 x 2 plus x 3 equals negative 1" display="block"><mrow><mn>2</mn> <msub><mi>x</mi> <mn>1</mn></msub> <mo>-</mo> <mn>4</mn> <msub><mi>x</mi> <mn>2</mn></msub> <mo>+</mo> <msub><mi>x</mi> <mn>3</mn></msub> <mo>=</mo> <mo>-</mo> <mn>1</mn></mrow></math>

这设置了通常的矩阵方程

<math alttext="bold upper A bold x equals bold b" display="block"><mrow><mi>𝐀𝐱</mi> <mo>=</mo> <mi>𝐛</mi></mrow></math>

where

<math alttext="bold x equals Start 1 By 3 Matrix 1st Row 1st Column x 1 2nd Column x 2 3rd Column x 3 EndMatrix Superscript sans-serif upper T" display="block"><mrow><mi>𝐱</mi> <mo>=</mo> <msup><mfenced open="[" close="]"><mtable><mtr><mtd><msub><mi>x</mi> <mn>1</mn></msub></mtd> <mtd><msub><mi>x</mi> <mn>2</mn></msub></mtd> <mtd><msub><mi>x</mi> <mn>3</mn></msub></mtd></mtr></mtable></mfenced> <mi>𝖳</mi></msup></mrow></math>

矩阵 <math alttext="bold upper A"><mi>𝐀</mi></math> 包含系数，列向量 <math alttext="bold b"><mi>𝐛</mi></math> 包含方程右侧的常数。LU 算法将矩阵 <math alttext="bold upper A"><mi>𝐀</mi></math> 分解为下三角和上三角矩阵的乘积 <math alttext="bold upper L"><mi>𝐋</mi></math> 和 <math alttext="bold upper U"><mi>𝐔</mi></math>，以便解 <math alttext="bold x"><mi>𝐱</mi></math> 。

在 Eigen 中，形成矩阵 <math alttext="bold upper A"><mi>𝐀</mi></math> 和向量 <math alttext="bold b"><mi>𝐛</mi></math>：

_

```
MatrixXd A		// Row-major
{
	{3.0, -5.0, 1.0},
	{-1.0, -1.0, 1.0},
	{2.0, -4.0, 1.0}
};

VectorXd b
{
	{0.0, -4.0, 1.0}
};
```

下一步是创建 `Eigen::FullPivLU` 类的实例，使用模板参数 `MatrixXd`。这设置了 LU 分解。要找到包含解的向量，只需在此对象上调用 `solve(.)` 成员函数。这意味着只需两行代码，如约定：

```
Eigen::FullPivLU<MatrixXd> lu(A);
VectorXd x = lu.solve(b);
```

解 `x` 的解法是（从上到下 <math alttext="x 1"><msub><mi>x</mi> <mn>1</mn></msub></math> , <math alttext="x 2"><msub><mi>x</mi> <mn>2</mn></msub></math> , <math alttext="x 3"><msub><mi>x</mi> <mn>3</mn></msub></math> ）

_

```
2.5
1.5
  0
```

Eigen 中还提供了其他几种分解方法来解线性系统，通常需要在速度和精度之间做出选择。上面示例中的 LU 分解在精度方面是最佳的，尽管还有其他一些可能更快但稳定性不同的方法。完整列表可在此处找到：

[基本线性求解](http://eigen.tuxfamily.org/dox/group__TutorialLinearAlgebra.html) **{20}**

Eigen 中的矩阵分解方法比较也可用：

[Eigen 提供的分解目录](http://eigen.tuxfamily.org/dox/group__TopicLinearAlgebraDecompositions.html) **{21}**

### 使用多元回归和奇异值分解进行基金跟踪

金融中另一个常见的编程问题是使用多元回归进行基金跟踪。例如

+   追踪一个对冲基金组合是否按照其声明的配置目标进行，通过将其回报与一组对冲基金风格指数的回归进行比较。

+   追踪投资组合对不同市场部门变化的敏感性。

+   追踪提供在保本投资产品（如可变年金）中的互惠基金的适应性，关于其各自的基金组指数。

在多元回归中，任务是找到一个向量

<math alttext="ModifyingAbove beta With caret equals Start 1 By 4 Matrix 1st Row 1st Column beta 1 2nd Column beta 2 3rd Column  ellipsis 4th Column beta Subscript n EndMatrix Superscript sans-serif upper T Baseline slash slash ModifyingAbove beta With caret equals Start 1 By 4 Matrix 1st Row 1st Column beta 1 2nd Column beta 2 3rd Column  ellipsis 4th Column beta Subscript n EndMatrix Superscript down-tack Baseline slash slash Start 1 By 4 Matrix 1st Row 1st Column beta 1 2nd Column beta 2 3rd Column  ellipsis 4th Column beta Subscript n Baseline EndMatrix slash slash ModifyingAbove beta With caret equals Start 1 By 4 Matrix 1st Row 1st Column beta 1 2nd Column beta 2 3rd Column  ellipsis 4th Column beta Subscript n Baseline EndMatrix slash slash ModifyingAbove beta With caret equals Start 1 By 4 Matrix 1st Row 1st Column beta 1 2nd Column beta 2 3rd Column  ellipsis 4th Column beta Subscript n EndMatrix Superscript sans-serif upper T Baseline slash slash Start 1 By 3 Matrix 1st Row 1st Column w Subscript t Superscript left-parenthesis 1 right-parenthesis Baseline 2nd Column  ellipsis 3rd Column w Subscript t Superscript left-parenthesis m right-parenthesis Baseline EndMatrix slash slash ModifyingAbove beta With caret equals left-bracket beta 1 beta 2 ellipsis beta Subscript n Baseline right-bracket Superscript sans-serif upper T" display="block"><mrow><mover accent="true"><mi>β</mi> <mo>^</mo></mover> <mo>=</mo> <msup><mfenced open="[" close="]"><mtable><mtr><mtd><msub><mi>β</mi> <mn>1</mn></msub></mtd> <mtd><msub><mi>β</mi> <mn>2</mn></msub></mtd> <mtd><mo>⋯</mo></mtd><mtd><msub><mi>β</mi> <mi>n</mi></msub></mtd></mtr></mtable></mfenced> <mi>𝖳</mi></msup> <mo>/</mo> <mo>/</mo> <mover accent="true"><mi>β</mi> <mo>^</mo></mover> <mo>=</mo> <msup><mfenced open="[" close="]"><mtable><mtr><mtd><msub><mi>β</mi> <mn>1</mn></msub></mtd> <mtd><msub><mi>β</mi> <mn>2</mn></msub></mtd> <mtd><mo>⋯</mo></mtd><mtd><msub><mi>β</mi> <mi>n</mi></msub></mtd></mtr></mtable></mfenced> <mi>⊤</mi></msup> <mo>/</mo> <mo>/</mo> <mfenced open="[" close="]"><mtable><mtr><mtd><msub><mi>β</mi> <mn>1</mn></msub></mtd> <mtd><msub><mi>β</mi> <mn>2</mn></msub></mtd> <mtd><mo>⋯</mo></mtd> <mtd><msub><mi>β</mi> <mi>n</mi></msub></mtd></mtr></mtable></mfenced> <mo>/</mo> <mo>/</mo> <mover accent="true"><mi>β</mi> <mo>^</mo></mover> <mo>=</mo> <mfenced open="[" close="]"><mtable><mtr><mtd><mrow><msub><mi>β</mi> <mn>1</mn></msub></mrow></mtd> <mtd><msub><mi>β</mi> <mn>2</mn></msub></mtd> <mtd><mo>⋯</mo></mtd> <mtd><msub><mi>β</mi> <mi>n</mi></msub></mtd></mtr></mtable></mfenced> <mo>/</mo> <mo>/</mo> <mover accent="true"><mi>β</mi> <mo>^</mo></mover> <mo>=</mo> <msup><mfenced open="[" close="]"><mtable><mtr><mtd><mrow><msub><mi>β</mi> <mn>1</mn></msub></mrow></mtd> <mtd><msub><mi>β</mi> <mn>2</mn></msub></mtd> <mtd><mo>⋯</mo></mtd><mtd><msub><mi>β</mi> <mi>n</mi></msub></mtd></mtr></mtable></mfenced> <mi>𝖳</mi></msup> <mo>/</mo> <mo>/</mo> <mfenced open="[" close="]"><mtable><mtr><mtd><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mn>1</mn><mo>)</mo></mrow></msubsup></mtd> <mtd><mo>⋯</mo></mtd> <mtd><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mi>m</mi><mo>)</mo></mrow></msubsup></mtd></mtr></mtable></mfenced> <mo>/</mo> <mo>/</mo> <mover accent="true"><mi>β</mi> <mo>^</mo></mover> <mo>=</mo> <msup><mrow><mo>[</mo><msub><mi>β</mi> <mn>1</mn></msub> <msub><mi>β</mi> <mn>2</mn></msub> <mo>⋯</mo><msub><mi>β</mi> <mi>n</mi></msub> <mo>]</mo></mrow> <mi>𝖳</mi></msup></mrow></math>

使得满足正规方程的矩阵形式

<math display="block"><mrow><mover><mi>𝛃</mi> <mo stretchy="false" style="math-style:normal;math-depth:0;">^</mo></mover> <mo>=</mo> <msup><mrow><mo fence="true" form="prefix">[</mo> <mtable columnalign="center"><mtr><mtd><mrow><msup><mi mathvariant="bold">X</mi> <mi>𝖳</mi></msup> <mi mathvariant="bold">X</mi></mrow></mtd></mtr></mtable> <mo fence="true" form="postfix">]</mo></mrow> <mrow><mo>−</mo> <mn>1</mn></mrow></msup> <msup><mi mathvariant="bold">X</mi> <mi>𝖳</mi></msup> <mi mathvariant="bold">Y</mi> <mo lspace="0em" rspace="0em">⁄</mo> <mo lspace="0em" rspace="0em">⁄</mo> <mover><mi>𝛃</mi> <mo stretchy="false" style="math-style:normal;math-depth:0;">^</mo></mover> <mo>=</mo> <mo form="prefix" stretchy="false">[</mo> <msup><mi mathvariant="bold">X</mi> <mi>𝖳</mi></msup> <mi mathvariant="bold">X</mi> <msup><mo form="postfix" stretchy="false">]</mo> <mrow><mo>−</mo> <mn>1</mn></mrow></msup> <msup><mi mathvariant="bold">X</mi> <mi>𝖳</mi></msup> <mi>𝐲</mi></mrow></math>

其中 <math alttext="bold upper X"><mi>𝐗</mi></math> 是包含 *p* 列自变量数据和 *n* 个观察（行）的设计矩阵，且观察数 *n* “舒适地”大于数据列数 *p*，以确保稳定性。这在这类基金追踪应用中通常是情况。

###### 注意

对于基金追踪应用程序，拦截项 <math alttext="beta 0"><msub><mi>β</mi> <mn>0</mn></msub></math> 通常可以被删除，因此在此处被省略。对于需要截距的回归案例，当使用 Eigen 时，需要在设计矩阵中附加一个全为 1 的最后一列。 _

一种常用的解决方案是紧凑的奇异值分解（SVD），它用分解 <math alttext="bold upper U bold upper Sigma bold upper V Superscript upper T"><mrow><mi>𝐔</mi> <mi>Σ</mi> <msup><mi>𝐕</mi> <mi>T</mi></msup></mrow></math> 替换矩阵 <math alttext="bold upper X"><mi>𝐗</mi></math>，其中 <math alttext="bold upper U"><mi>𝐔</mi></math> 是一个 <math alttext="n times p"><mrow><mi>n</mi> <mo>×</mo> <mi>p</mi></mrow></math> 矩阵，<math alttext="bold upper V"><mi>𝐕</mi></math> 是 <math alttext="p times p"><mrow><mi>p</mi> <mo>×</mo> <mi>p</mi></mrow></math> 矩阵，<math alttext="bold upper Sigma"><mi>Σ</mi></math> 是一个由严格正数值构成的 <math alttext="p times p"><mrow><mi>p</mi> <mo>×</mo> <mi>p</mi></mrow></math> 对角矩阵。将这一替换应用到 <math alttext="ModifyingAbove beta With caret"><mover accent="true"><mi>β</mi> <mo>^</mo></mover></math> 的原始公式中，我们得到

<math alttext="ModifyingAbove beta With caret equals bold upper V bold upper Sigma bold upper U Superscript sans-serif upper T Baseline bold y" display="block"><mrow><mover accent="true"><mi>β</mi> <mo>^</mo></mover> <mo>=</mo> <mi>𝐕</mi> <mi>Σ</mi> <msup><mi>𝐔</mi> <mi>𝖳</mi></msup> <mi>𝐲</mi></mrow></math>

Eigen 提供了两个可用于获取回归系数最小二乘估计的 SVD 求解器。其中之一，如 Eigen 文档中所述，是 [Jacobi SVD decomposition of a rectangular matrix_](https://eigen.tuxfamily.org/dox-devel/classEigen_1_1JacobiSVD.html) **{22}**。文档还指出，Jacobi 版本建议用于设计矩阵列数不超过 16 列的情况，这在某些基金追踪问题中已足够。

这种工作方式是，在给定设计矩阵预测数据包含在 `MatrixXd X` 中时，Eigen 在类模板 `Eigen::JacobiSVD` 中设置了 SVD 逻辑。然后，给定响应数据包含在 `VectorXd Y` 中，求解 <math alttext="ModifyingAbove beta With caret"><mover accent="true"><mi>β</mi> <mo>^</mo></mover></math> 只需两行代码：

```
Eigen::JacobiSVD<MatrixXd> svd{ X, Eigen::ComputeThinU | Eigen::ComputeThinV };
VectorXd beta = svd.solve(Y);
```

###### 注意

`Eigen::ComputeThinU | Eigen::ComputeThinV` 的按位或参数指示程序使用 SVD 的紧凑版本。如果需要完整的 SVD，则以上`JacobiSVD`实例化将是：

```
Eigen::JacobiSVD<MatrixXd> svd{ X, Eigen::ComputeFullU | Eigen::ComputeFullV };
```

在这种情况下，<math alttext="bold upper U"><mi>𝐔</mi></math> 矩阵将是 <math alttext="n times n"><mrow><mi>n</mi> <mo>×</mo> <mi>n</mi></mrow></math> ，<math alttext="bold upper Sigma"><mi>Σ</mi></math> 将是 <math alttext="n times p"><mrow><mi>n</mi> <mo>×</mo> <mi>p</mi></mrow></math> 伪逆。

没有默认设置。

例如，假设我们有三个行业 ETF，并希望研究它们与更广泛市场（如标准普尔 500 指数）的关系，假设我们有 30 天的每日观察数据。

设计矩阵将包含三个 ETF 回报，并存储在名为`X`的`MatrixXd`中，如下所示：

```
MatrixXd X{ 3, 30 };	// 3 sector funds, 30 observations (will transpose)
X	<<
	// Sector fund 1
	-0.044700388, -0.007888394, 0.042980064, 0.016416586, -0.01779658, -0.016714149,
	0.019472031, 0.029853293, 0.023126097, -0.033879088, -0.00338369, -0.018474493,
	-0.012509815, -0.01834808, 0.010626754, 0.036669407, 0.010811115, -0.035571742,
	0.027474007, 0.005406069, -0.010159427, -0.006145632, -0.0103273, -0.010435171,
	0.011127197, -0.023793709, -0.028009362, 0.00218235, 0.008683152, 0.001440032,

	// Sector fund 2
	-0.019002703, 0.026036835, 0.03782709, 0.010629292, -0.008382267, 0.001121697,
	-0.004494407, 0.017304537, -0.006106293, 0.012174645, -0.003305029, 0.027219671,
	-0.036089287, -0.00222959, -0.015748493, -0.02061919, -0.011641386, 0.023148757,
	-0.002290732, 0.006288094, -0.012038397, -0.029258743, 0.011219297, -0.008846992,
	-0.033738048, 0.02061908, -0.012077677, 0.015672887, 0.041012907, 0.052195282,

	// Sector fund 3
	-0.030629136, 0.024918984, -0.001715798, 0.008561614, 0.003406931, -0.010823864,
	-0.010361097, -0.009302434, 0.008142014, -0.004064208, 0.000584335, 0.004640294,
	0.031893332, -0.013544321, -0.023573641, -0.004665085, -0.006446259, -0.005311412,
	0.045096308, -0.007374697, -0.00514201, -0.001715798, -0.005176363, -0.002884991,
	0.002309361, -0.014521608, -0.017711709, 0.001192088, -0.00238233, -0.004395918;

X.transposeInPlace();
```

类似地，市场回报以`VectorXd`数组`Y`存储：

```
VectorXd Y{ 30 };	// 30 observations of market returns
Y <<
	-0.039891316, 0.00178709, -0.0162018, 0.056452057, 0.00342504, -0.012038314,
	-0.009997657, 0.013452043, 0.013485674, -0.007898137, 0.008111428, -0.015424523,
	-0.002161451, -0.028752191, 0.011292655, -0.007958389, -0.004002386, -0.031690771,
	0.026776892, 0.009803957, 0.000886608, 0.01495181, -0.004155781, -0.001535225,
	0.013517306, -0.021228542, 0.001988701, -0.02051788, 0.005841347, 0.011248933;
```

获得回归系数只是通过编译和运行在一开始显示的两行代码，这给我们`beta`：

```
  0.352339
-0.0899004
  0.391252
```

如果需要，还可以使用以下访问器函数获得<math alttext="bold upper U"><mi>𝐔</mi></math> 和 <math alttext="bold upper V"><mi>𝐕</mi></math> 矩阵，以及 <math alttext="bold upper Sigma"><mi>Σ</mi></math> 矩阵：

```
cout << svd.matrixU() << endl;			// U: n x p = 30 x 3
cout << svd.matrixV() << endl;			// V: p x p = 3 x 3
cout << svd.singularValues().asDiagonal() << endl;		// Sigma: p x p = 3 x 3
```

另一种可选的 SVD 求解器，[*Bidiagonal Divide and Conquer SVD*](https://eigen.tuxfamily.org/dox-devel/classEigen_1_1BDCSVD.html)，**{23}** 也可在 Eigen 中找到。根据文档，推荐用于设计矩阵列数大于 16 的情况以获得更好的性能。

设置与 Jacobi 情况相同，但使用`JacobiSVD`的位置上的`BDCSVD`类：

```
Eigen::BDCSVD<MatrixXd> svd(X, Eigen::ComputeThinU | Eigen::ComputeThinV);
VectorXd beta = svd.solve(Y);
```

值得注意的是 Eigen 还提供 QR 分解以及使用 Eigen 矩阵和操作进行正常方程求解的能力。如文档所述，[*解线性最小二乘系统*](https://eigen.tuxfamily.org/dox-devel/group__LeastSquares.html)，这些方法可能比 SVD 方法更快，但可能精度较低。**{24}** 如果速度是问题，这些都是您可以考虑的备选方案，但需要在适当的条件下使用。

### 相关的随机股票路径和乔列斯基分解

在金融中，乔列斯基分解是生成相关的蒙特卡洛股票路径模拟的一种常用工具。例如，在定价篮子期权时，需要考虑篮子证券运动之间的协方差，特别是在生成相关的随机正态抽取时。这与生成单个基础证券的随机价格路径形成对比，正如我们在第八章中看到的那样。

<math alttext="upper S Subscript t Baseline equals upper S Subscript t minus 1 Baseline e Superscript left-parenthesis StartFraction r minus sigma squared Over 2 EndFraction right-parenthesis normal upper Delta t plus sigma epsilon Super Subscript t Superscript StartRoot normal upper Delta t EndRoot" display="block"><mrow><msub><mi>S</mi> <mi>t</mi></msub> <mo>=</mo> <msub><mi>S</mi> <mrow><mi>t</mi><mo>-</mo><mn>1</mn></mrow></msub> <msup><mi>e</mi> <mrow><mrow><mo>(</mo><mfrac><mrow><mi>r</mi><mo>-</mo><msup><mi>σ</mi> <mn>2</mn></msup></mrow> <mn>2</mn></mfrac><mo>)</mo></mrow><mi>Δ</mi><mi>t</mi><mo>+</mo><mi>σ</mi><msub><mi>ε</mi> <mi>t</mi></msub> <msqrt><mrow><mi>Δ</mi><mi>t</mi></mrow></msqrt></mrow></msup></mrow></math>

其中再次 <math alttext="epsilon Subscript t Baseline tilde upper N left-parenthesis 0 comma 1 right-parenthesis"><mrow><msub><mi>ε</mi> <mi>t</mi></msub> <mo>∼</mo> <mi>N</mi> <mrow><mo>(</mo> <mn>0</mn> <mo>,</mo> <mn>1</mn> <mo>)</mo></mrow></mrow></math>，<math alttext="sigma"><mi>σ</mi></math> 是股票的波动率，<math alttext="r"><mi>r</mi></math> 表示无风险利率，

在篮子期权的情况下，现在我们需要为每个资产（总数为 *m* 个）在每个时间点 <math alttext="t"><mi>t</mi></math> 生成一条路径，其中术语 <math alttext="sigma epsilon Subscript t"><mrow><mi>σ</mi> <msub><mi>ε</mi> <mi>t</mi></msub></mrow></math> 被一个随机项 <math alttext="w Subscript t Superscript left-parenthesis i right-parenthesis"><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mi>i</mi><mo>)</mo></mrow></msubsup></math> 取代，这个随机项再次基于标准正态分布抽取，但其波动也包含与篮子中其他资产的相关性。因此，我们需要生成一组价格 <math alttext="StartSet upper S Subscript t Superscript left-parenthesis i right-parenthesis Baseline EndSet"><mfenced separators="" open="{" close="}"><msubsup><mi>S</mi> <mi>t</mi> <mrow><mo>(</mo><mi>i</mi><mo>)</mo></mrow></msubsup></mfenced></math>，其中

<math alttext="upper S Subscript t Superscript left-parenthesis 1 right-parenthesis Baseline equals upper S Subscript t minus 1 Superscript left-parenthesis 1 right-parenthesis Baseline e Superscript left-parenthesis StartFraction r minus sigma squared Over 2 EndFraction right-parenthesis normal upper Delta t plus w Super Subscript t Super Superscript left-parenthesis 1 right-parenthesis Superscript StartRoot normal upper Delta t EndRoot Baseline left-parenthesis asterisk right-parenthesis" display="block"><mrow><msubsup><mi>S</mi> <mi>t</mi> <mrow><mo>(</mo><mn>1</mn><mo>)</mo></mrow></msubsup> <mo>=</mo> <msubsup><mi>S</mi> <mrow><mi>t</mi><mo>-</mo><mn>1</mn></mrow> <mrow><mo>(</mo><mn>1</mn><mo>)</mo></mrow></msubsup> <msup><mi>e</mi> <mrow><mrow><mo>(</mo><mfrac><mrow><mi>r</mi><mo>-</mo><msup><mi>σ</mi> <mn>2</mn></msup></mrow> <mn>2</mn></mfrac><mo>)</mo></mrow><mi>Δ</mi><mi>t</mi><mo>+</mo><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mn>1</mn><mo>)</mo></mrow></msubsup> <msqrt><mrow><mi>Δ</mi><mi>t</mi></mrow></msqrt></mrow></msup> <mrow><mo>(</mo> <mo>*</mo> <mo>)</mo></mrow></mrow></math><math alttext="period" display="block"><mo>。</mo></math><math alttext="period" display="block"><mo>。</mo></math><math alttext="period" display="block"><mo>。</mo></math><math alttext="upper S Subscript t Superscript left-parenthesis m right-parenthesis Baseline equals upper S Subscript t minus 1 Superscript left-parenthesis m right-parenthesis Baseline e Superscript left-parenthesis StartFraction r minus sigma squared Over 2 EndFraction right-parenthesis normal upper Delta t plus w Super Subscript t Super Superscript left-parenthesis m right-parenthesis Superscript StartRoot normal upper Delta t EndRoot" display="block"><mrow><msubsup><mi>S</mi> <mi>t</mi> <mrow><mo>(</mo><mi>m</mi><mo>)</mo></mrow></msubsup> <mo>=</mo> <msubsup><mi>S</mi> <mrow><mi>t</mi><mo>-</mo><mn>1</mn></mrow> <mrow><mo>(</mo><mi>m</mi><mo>)</mo></mrow></msubsup> <msup><mi>e</mi> <mrow><mrow><mo>(</mo><mfrac><mrow><mi>r</mi><mo>-</mo><msup><mi>σ</mi> <mn>2</mn></msup></mrow> <mn>2</mn></mfrac><mo>)</mo></mrow><mi>Δ</mi><mi>t</mi><mo>+</mo><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mi>m</mi><mo>)</mo></mrow></msubsup> <msqrt><mrow><mi>Δ</mi><mi>t</mi></mrow></msqrt></mrow></msup></mrow></math>

对于每个资产 <math alttext="i equals 1 comma ellipsis comma m"><mrow><mi>i</mi> <mo>=</mo> <mn>1</mn> <mo>,</mo> <mo>⋯</mo> <mo>,</mo> <mi>m</mi></mrow></math> 在每个时间步 <math alttext="t Subscript j Baseline comma j equals 1 comma ellipsis comma n"><mrow><msub><mi>t</mi> <mi>j</mi></msub> <mo>,</mo> <mi>j</mi> <mo>=</mo> <mn>1</mn> <mo>,</mo> <mo>⋯</mo> <mo>,</mo> <mi>n</mi></mrow></math> 。我们的任务是计算随机但相关的向量

<math alttext="Start 1 By 3 Matrix 1st Row 1st Column w Subscript t Superscript left-parenthesis 1 right-parenthesis 2nd Column  ellipsis 3rd Column w Subscript t Superscript left-parenthesis m right-parenthesis EndMatrix Superscript sans-serif upper T" display="block"><msup><mfenced open="[" close="]"><mtable><mtr><mtd><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mn>1</mn><mo>)</mo></mrow></msubsup></mtd> <mtd><mo>⋯</mo></mtd><mtd><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mi>m</mi><mo>)</mo></mrow></msubsup></mtd></mtr></mtable></mfenced> <mi>𝖳</mi></msup></math>

对于每个时间 <math alttext="t"><mi>t</mi></math>。这就是 Cholesky 分解的作用。对于一个 <math alttext="n times n"><mrow><mi>n</mi> <mo>×</mo> <mi>n</mi></mrow></math> 协方差矩阵 <math alttext="bold upper Sigma"><mi>Σ</mi></math>，假设它是正定的，将有一个 Cholesky 分解

<math alttext="bold upper Sigma equals upper L upper L Superscript sans-serif upper T" display="block"><mrow><mi>Σ</mi> <mo>=</mo> <mi>L</mi> <msup><mi>L</mi> <mi>𝖳</mi></msup></mrow></math>

其中 *L* 是一个下三角矩阵。然后，对于标准正态变量向量 <math alttext="bold z"><mi>𝐳</mi></math>，

<math alttext="Start 1 By 4 Matrix 1st Row 1st Column z 1 2nd Column z 2 3rd Column  ellipsis 4th Column z Subscript m EndMatrix Superscript sans-serif upper T" display="block"><msup><mfenced open="[" close="]"><mtable><mtr><mtd><msub><mi>z</mi> <mn>1</mn></msub></mtd> <mtd><msub><mi>z</mi> <mn>2</mn></msub></mtd> <mtd><mo>⋯</mo></mtd><mtd><msub><mi>z</mi> <mi>m</mi></msub></mtd></mtr></mtable></mfenced> <mi>𝖳</mi></msup></math>

由 <math alttext="n times 1"><mrow><mi>n</mi> <mo>×</mo> <mn>1</mn></mrow></math> 向量生成的 <math alttext="bold upper L bold z Superscript sans-serif upper T"><mrow><mi>𝐋</mi> <msup><mi>𝐳</mi> <mi>𝖳</mi></msup></mrow></math> 将提供一组相关波动率，可以用来生成每个基础证券的价格的随机场景。对于每个时间步 <math alttext="t"><mi>t</mi></math>，我们用 <math alttext="bold z"><mi>𝐳</mi></math> 替换为 <math alttext="bold z Subscript bold t"><msub><mi>𝐳</mi> <mi>𝐭</mi></msub></math>，从而得到我们想要的结果：

<math alttext="bold w Subscript bold t Baseline equals bold upper L bold z Subscript t Superscript sans-serif upper T" display="block"><mrow><msub><mi>𝐰</mi> <mi>𝐭</mi></msub> <mo>=</mo> <msubsup><mi>𝐋𝐳</mi> <mi>t</mi> <mi>𝖳</mi></msubsup></mrow></math>

然后，通过将每个向量 <math alttext="bold z Subscript bold t"><msub><mi>𝐳</mi> <mi>𝐭</mi></msub></math> 放置到矩阵的一列中，比如 <math alttext="bold upper Z"><mi>𝐙</mi></math>，可以扩展任意数量的 n 个时间步骤。然后，我们可以一次生成一整套相关随机变量向量，并将结果放置在一个矩阵中

<math alttext="bold upper W equals bold upper L bold upper Z" display="block"><mrow><mi>𝐖</mi> <mo>=</mo> <mi>𝐋𝐙</mi></mrow></math>

Eigen 提供了对 `MatrixXd` 对象的 Cholesky 分解，使用 `Eigen::LLT` 类模板和参数 `MatrixXd`。它再次包括创建这个类的对象，然后调用成员函数 `matrixL`，该函数返回上述矩阵 <math alttext="bold upper L"><mi>𝐋</mi></math>。

举个例子，假设我们篮子里有四种证券，其协方差矩阵如下。

```
MatrixXd cov_basket
{
    { 0.01263, 0.00025, -0.00017, 0.00503},
    { 0.00025, 0.00138,  0.00280, 0.00027},
    {-0.00017, 0.00280,  0.03775, 0.00480},
    { 0.00503, 0.00027,  0.00480, 0.02900}
};
```

当使用矩阵数据构造 `Eigen::LLT` 对象时，Cholesky 分解被设置。调用成员函数 `matrixL()` 计算分解并返回结果的下三角矩阵：

```
Eigen::LLT<Eigen::MatrixXd> chol{ cov_basket };
MatrixXd chol_mtx = chol.matrixL();
```

这给了我们

```
    0.1124          0          0          0
  0.002226  0.0370332          0          0
-0.0015544  0.0756179   0.178975          0
 0.0447889 0.00464393  0.0252348   0.162289
```

现在假设在蒙特卡罗模型中将有六个时间步长，持续一年。这意味着我们将需要六个包含四个标准正态变量的向量。为此，我们可以使用标准库 `<random>` 函数生成一个 4 <math alttext="times"><mo>×</mo></math> 6 矩阵，并将随机向量放置在连续的列中。

首先，创建一个有四行六列的 `MatrixXd` 对象：

```
MatrixXd corr_norms{ 4, 6 };
```

首先，将此矩阵用无关的标准正态分布填充。如我们之前所做（第八章），我们可以设置一个随机引擎和分布，并将它们捕获在一个 lambda 函数中以生成标准正态变量：

```
std::mt19937_64 mt_norm{ 100 };		// Seed is arbitrary, just set to 100 again
std::normal_distribution<> std_nd;

auto std_norm = &mt_norm, &std_nd
{
    return std_nd(mt_norm);
};
```

因为每个`MatrixXd`中的列可以被视为`VectorXd`，我们可以再次通过矩阵按列进行迭代，并在基于范围的`for`循环中对每列应用`std::ranges::transform`算法。

```
for (auto col : corr_norms.colwise())
{
    std::ranges::transform(col,
        col.begin(), std_norm);
}
```

此中期结果将类似于以下内容，实际结果取决于编译器（在本例中使用 Microsoft Visual Studio 2022 编译器）：

```
   0.201395    0.197482     1.22857     1.40751     1.82789   -0.150014
 -0.0769593   0.0830647     1.86252    0.122389   -0.949222    0.667817
   0.936051     1.16233   -0.642932    0.538005    -1.82688   -0.451039
-0.00916217    -2.79186   -0.434655  -0.0553752     1.46312    0.345527
```

然后，为了得到*相关*正常值，将上述结果乘以 Cholesky 矩阵，并将`corr_norms`重新分配为结果。

```
MatrixXd corr_norms = chol_mtx * corr_norms;
```

这个结果对应于数学推导中的矩阵<math alttext="bold upper W"><mi>𝐖</mi></math>，其结果如下：

```
  0.0226368   0.0221969    0.138091    0.158204    0.205455  -0.0168616
-0.00240174  0.00351574   0.0717097  0.00766557  -0.0310838   0.0243974
   0.161397    0.214002   0.0238613    0.103356   -0.401585  -0.0299926
  0.0307971   -0.414526  -0.0230881   0.0681989    0.268808   0.0410757
```

`corr_norms`中的每个连续列将提供一组四个相关随机变量，可以替换(*)中每个时间点<math alttext="w Subscript t Superscript left-parenthesis 1 right-parenthesis Baseline ellipsis w Subscript t Superscript left-parenthesis 4 right-parenthesis"><mrow><msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mn>1</mn><mo>)</mo></mrow></msubsup> <mo>⋯</mo> <msubsup><mi>w</mi> <mi>t</mi> <mrow><mo>(</mo><mn>4</mn><mo>)</mo></mrow></msubsup></mrow></math>。首先，我们需要四个基础股票的现货价格，可以存储在 Eigen 的`VectorXd`中（假设它们来自实时市场数据源）。例如：

```
VectorXd spots(4);          // Init spot prices from market
spots << 100.0, 150.0, 25.0, 50.0;
```

接下来，我们需要一个矩阵来存储每个股票的随机价格路径，从每个现货价格开始，在额外的第一列中存储，使其成为一个 4 <math alttext="times"><mo>×</mo></math> 7 矩阵。

```
MatrixXd integ_scens{ corr_norms.rows(), corr_norms.cols() + 1 }	// 4 x 7 matrix
integ_scens.col(0) = spots;
```

假设到期时间为一年，分为六个等时间步。因此，我们可以得到<math alttext="normal upper Delta t"><mrow><mi>Δ</mi> <mi>t</mi></mrow></math>值，比如`dt`。

```
double time_to_maturity = 1.0;
unsigned num_time_steps = 6;
double dt = time_to_maturity / num_time_steps;
```

如(*)中所示，每个给定基础股票的每个连续价格可以在 lambda 函数中计算，其中`price`是场景中的前期价格，而`vol`是特定股票的波动率。

```
auto gen_price = dt, rf_rate -> double
{
    double expArg1 = (rf_rate - ((vol * vol) / 2.0)) * dt;
    double expArg2 = corr_norm * std::sqrt(dt);
    double next_price = price * std::exp(expArg1 + expArg2);
    return next_price;
};
```

最后，在每个时间步骤的每个基础股票上，我们可以设置一个迭代，并在每个步骤调用 lambda 函数：

```
for (unsigned j = 1; j < integ_scens.cols(); ++j)
{
    for (unsigned i = 0; i < integ_scens.rows(); ++i)
    {
        integ_scens(i, j) = gen_price(integ_scens(i, j - 1), vols(i), corr_norms(i, j - 1));
    }
}
```

对于此示例，结果如下：

```
100		100.99		101.972		107.952		115.226		125.384		124.6
150		150.086 	150.535 	155.248 	155.976 	154.248 	156.034
25 		26.6633 	29.0545 	29.2955 	30.5129 	25.8607 	25.5082
50 		50.5946 	42.6858 	42.2536  	43.414 		48.4132 	49.1949
```

再次提醒，由于不同供应商的标准库版本中`<random>`实现的差异，结果可能会有所不同。

### 收益率曲线动态和主成分分析

主成分分析（PCA）是确定驱动收益率曲线形状变化的变动源和幅度的工具。通过首先计算跨多个债券到期期限的每日收益率变化的协方差矩阵的特征值，并按降序排序，然后通过将每个特征值除以所有特征值的总和来计算权重。

实证研究表明，前三个特征值的贡献几乎构成了权重的全部，其中第一个权重对应于收益曲线的平行移动，第二个对应于其“倾斜”或“斜率”的变化，第三个对应于曲率。关于这一点的原因和详细内容可以在 Ruppert 和 Matteson 的计算金融优秀著作第十八章中找到 **{25}**，以及 Rebonato 关于利率衍生品的经典文本第三章中 **{26}**。

存在严格的统计检验来衡量显著性，但仅权重本身可以提供每个变化来源的相对估计量。

Ruppert 和 Matteson 的文本第 18.2 节 {op cit 25} 提供了一个关于如何应用主成分分析到公开可用的美国国债收益数据的优秀示例 {put URL here}。结果的协方差矩阵 — 作为下述`MatrixXd`对象的构造器数据，基于十一种不同到期限的美国国债收益率的波动，从一个月到 30 年。基础数据来自 1990 年 1 月到 2008 年 10 月的时期。

要计算特征值，首先将协方差矩阵数据加载到`MatrixXd`实例的上三角区域。

```
	MatrixXd term_struct_cov_mtx
	{
		// 1 month
		{ 0.018920,	0.009889, 0.005820,	0.005103, 0.003813,	0.003626,
			0.003136, 0.002646, 0.002015, 0.001438, 0.001303 },

		// 3 months
		{ 0.0, 0.010107, 0.006123, 0.004796, 0.003532, 0.003414,
			0.002893, 0.002404, 0.001815, 0.001217, 0.001109},

		// 6 months
		{ 0.0, 0.0, 0.005665, 0.004677, 0.003808, 0.003790,
			0.003255, 0.002771, 0.002179, 0.001567, 0.001400 },

		// 1 year
			{ 0.0, 0.0, 0.0, 0.004830, 0.004695, 0.004672,
				0.004126, 0.003606, 0.002952, 0.002238, 0.002007},

		// 2 years
		{ 0.0, 0.0, 0.0, 0.0, 0.006431, 0.006338,
			0.005789, 0.005162, 0.004337, 0.003343, 0.003004},

		// 3 years
		{ 0.0, 0.0, 0.0, 0.0, 0.0, 0.006524,
			0.005947, 0.005356, 0.004540, 0.003568, 0.003231 },

		// 5 years
		{ 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,
			0.005800, 0.005291, 0.004552, 0.003669, 0.003352 },

		// 7 years
		{ 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,
			0.0, 0.004985, 0.004346, 0.003572, 0.003288 },

		// 10 years
		{ 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,
			0.0, 0.0, 0.003958, 0.003319, 0.003085 },

		// 20 years
		{ 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,
			0.0, 0.0, 0.0, 0.003062, 0.002858 },

		// 30 years
		{ 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,
			0.0, 0.0, 0.0, 0.0, 0.002814 }
	};
```

由于实对称矩阵在无复杂分量时是平凡自共轭的，Eigen 可以将`selfadjointView<.>()`函数应用于上（或下）三角矩阵，以定义对称协方差矩阵的视图。然后，可以通过在对称结果上应用`eigenvalues()`函数来获得特征值（全部为实数）：

```
VectorXd eigenvals = term_struct_cov_mtx.selfadjointView<Eigen::Upper>().eigenvalues();
```

为了确定每个主成分的权重，我们需要将每个特征值除以所有特征值的总和：

```
double total_ev = std::accumulate(eigenvals.cbegin(), eigenvals.cend(), 0.0);
std::ranges::transform(eigenvals, eigenvals.begin(),
	total_ev {return x / total_ev; });
```

最后，为了按主成分顺序查看结果，需要将加权值从大到小排列：

```
std::ranges::sort(eigenvals, std::greater{});
```

检查`eigenvals`的内容，我们会发现以下结果：

```
0.67245 0.213209 0.073749
0.023811 0.00962511 0.00275773
0.0016744 0.00114298 0.000740139
0.000528862 0.000311391
```

由此可见，平行移动和收益曲线的“倾斜”效应是主导效应，相对权重分别为 67.2%和 21.3%，而曲率效应较小，为 7.4%。

# 未来方向：标准库中的线性代数

已向 ISO C++委员会提交了三个与线性代数相关的提案。

+   `std::mdspan`：[一个多态多维数组引用（P0009）](https://wg21.link/p0009) **{28}**

+   [基于 BLAS 的自由函数线性代数接口（P1673）](https://wg21.link/p1673) **{29}**

+   [向 C++标准库添加线性代数支持（P1385）](https://wg21.link/p1385) **{30}**

`mdspan`（P0009）可以在对容器的引用上施加多维数组结构，例如 STL `vector`。使用包含数据的`vector`和表示矩阵的引用`mdspan`作为示例，在构造`mdspan`时设置行数和列数。`mdspan`也可以采用更高维度的数组形式，但我们的目的是关注矩阵表示的二维情况。`mdspan`正式计划在 C++23 中发布。

第二个提案（P1673）是为外部线性代数提供标准接口，“基于密集型基本线性代数子程序（BLAS）”，对应于“BLAS 标准的子集”。换句话说，无论使用哪个外部线性代数库，代码都可以独立编写，从而使代码维护更加容易且不易出错。正如提案中所述，“接口的设计以 C++标准库的算法精神为基础”，并使用“mdspan……表示矩阵和向量”（同上）。此外，“接口的设计以 C++标准库的算法精神为基础”（同上）。目前计划在 C++26 中发布。

第三个提案（P1385）是在标准库中提供实际的 BLAS 类型功能。其主要目标是“为表示与线性代数相关的数学对象和基本操作提供矩阵词汇类型”（见 30 页）。该提案目前也计划在 C++26 中发布。

## mdspan（P0009）

如上所述，为了表示矩阵，`mdspan`建立对连续容器的引用，然后强加行数和列数。如果这些参数在编译时已知，创建`mdspan`很容易：

```
vector<int> v{ 101, 102, 103, 104, 105, 106 };
auto mds1 = std::mdspan{ v.data(), 3, 2 };
```

注意，`mdspan`使用`vector`的`data()`成员函数访问其内容。

在`mdspan`术语中，行和列被称为*extents*，通过每个索引访问行数和列数，行数索引为 0，列数索引为 1。总的*rank*表示为 2，对于更高阶的多维数组，*rank*会大于两个。

```
size_t n_rows{ mds1.extent(0) };		// 3
size_t n_cols{ mds1.extent(1) };		// 2
size_t n_extents{ mds1.rank() };		// 2
```

###### 注意

术语*rank*在应用于`mdspan`时与矩阵秩的数学定义不同。这种命名可能会令人困惑，但这是需要注意的事项。

在 C++23 中，`mdspan`对象的元素可以通过另一个新功能访问，即具有多个索引的方括号运算符：

```
for (size_t i = 0; i < mds1.extent(0); ++i)
{
	for (size_t j = 0; j < mds1.extent(1); ++j)
		cout << mds1[i, j] << "\t";

	cout << "\n";
}
```

这是一个受欢迎的改进，因为不再需要将每个索引放在单独的括号对中，就像 C 风格数组的情况一样。

```
double ** a[3][2];		// Ugh

// Could put a[2, 1] if it were an mdspan instead:
a[2][1] = 5.4;

...

delete [] a;
```

上述嵌套循环的运行时结果显示一个 3 <math alttext="times"><mo>×</mo></math> 2 矩阵：

```
101     102
103     104
105     106
```

也可以使用相同的数据定义具有不同维度的矩阵：

```
auto mds2 = std::mdspan(v.data(), 2, 3);		// 2 x 3
```

应用上述相同的循环，但将`mds1`替换为`mds2`，则会如预期显示：

```
101     102     103
104     105     106
```

需要小心的一点是修改`mdspan`对象或原始`vector`中的数据将由于两者之间的引用关系而同时更改。例如，修改向量的最后一个元素

```
v[5] = 874;
```

将显示在两个`mdspan`对象中。`mds1`变成

```
101     102
103     104
105     874
```

和`mds2`现在是

```
101     102     103
104     105     874
```

同样，更改`mds2`中的元素值将反映在`mds1`和向量`v`中。

在量化金融应用中，往往情况是在编译时不知道固定的行数和列数。假设`m`和`n`是在运行时确定的行和列数。这些维度可以通过在前面示例中的固定设置 2 和 3 处动态设置来动态设置

```
auto mds2 = std::mdspan(v.data(), 2, 3);
```

用`std::extents{m, n}`对象表示，如此处`mdspan`定义所示：

```
void dynamic_mdspan(size_t m, size_t n, vector<double> vec)
{

	std::mdspan md{ vec.data(), std::extents{m, n} };

	. . .

}
```

`std::extents{m, n}`参数表示每个 extent（即每行（`m`）和列（`n`））中的元素数量，这些在运行时动态确定。

假设我们在运行时有以下数据集：

```
vector<double> w{ 10.1, 10.2, 10.3, 10.4, 10.5, 10.6 };
size_t m{3};
size_t n{2};
```

使用这些作为`dynamic_mdspan(.)`函数的输入，然后将生成一个 3 <math alttext="times"><mo>×</mo></math> 2 `mdspan`矩阵：

```
10.1    10.2
10.3    10.4
10.5    10.6
```

###### 注意

上面的例子利用了*类模板自动推导*（CTAD）。`mdspan`和`extents`都是类模板，但是因为上面示例中的`w`是`vector`的`double`类型，而`vector`的大小是`size_t`类型，当替换为`dynamic_mdspan`函数中的`vec`时，编译器将推断`mdspan`和`extents`对象要使用`double`和`size_t`作为模板参数。

没有 CTAD，函数将写成如下形式：

```
template<typename T, typename S>
void dynamic_mdspan(S m, S n, T vec)
{
	using ext_t = std::extents<S, std::dynamic_extent, std::dynamic_extent>;
	std::mdspan<T, ext_t> mds_dyn{ vec.data(), m, n };

	. . .

}
```

本章的讨论将依赖于 CTAD，但在需要更一般性的情况下，将需要完全书写模板参数。

还有一件事要注意的是，在迄今为止的每个例子中，`mdspan`将默认按行主顺序排列数据。通过定义一个`layout_left`策略*映射*，在本例中称为`col_major`，可以将其排列为列主顺序，如下所示：

```
std::layout_left::mapping col_major{ std::extents{ m, n } };
```

然后可以通过在`mdspan`构造函数的 extents 参数中替换此映射来定义相应的列主矩阵。

```
std::mdspan md{ v.data(), col_major };
```

结果是矩阵的列主版本：

```
10.1    10.4
10.2    10.5
10.3    10.6
```

###### 注意

可以使用`std::layout_right`映射来明确设置行主顺序。

`mdspan`提案还包括一个名为`submdspan(.)`的“切片”函数，用于返回由 mdspan 表示的矩阵的单个行或列的引用。更一般地说，这将扩展到更高维数组的子集。

返回到行主整数 3 <math alttext="times"><mo>×</mo></math> 2 `mds1`示例，如果我们想要提取对第一行（索引 0）的引用，可以如下获得，使用`submdspan`中的第一个 extent（行）参数中的索引：

```
auto row_1 = std::submdspan(mds1, 0, std::full_extent)
```

这将给我们：

```
row_1[0] = 101  row_1[1] = 102
```

第 2 和第 3 行也可以通过将 `0` 替换为 `1` 和 `2` 进行引用。

通过将第二个（列）范围参数显式设置为列大小减 1，我们还可以访问最后一列：

```
auto col_last = std::submdspan(mds1, std::full_extent, mds1.extent(1)-1);
```

这将包括：

```
col_2[0] = 102  col_2[1] = 104  col_2[2] = 106
```

因为 `submdspan` 是指向包含行或列的 `mdspan` 的引用（视图），所以不需要生成额外的 `mdspan`，与从 `valarray` 中取得的 `slice_array` 不同。可以对 `submdspan` 应用在 `mdspan` 上的任何公共成员函数。然而，由于其引用性质，不包括提供给 `valarray` 的向量化数学运算符或函数，尽管在 `P1673` 中将提供运算符的另一种方法，该方法将在下一节讨论。

另一方面，由于其是一个引用，修改 `submdspan` 的元素也会修改底层的 `mdspan` 对象。假设重置 `col_last` 中的最后一个元素：

```
col_2[2] = 3333;
```

然后原始的 `mds1` 将变为：

```
101     102
103     104
105     3333
```

最后一点是关于多维数组（P1684）的提议，称为 `mdarray`，也正在审核中。如在 [提议](https://wg21.link/p1684) **{31}** 中所述，“`mdarray` 尽可能与 `mdspan` 相似，但其具有容器语义而不是引用语义”。换句话说，`mdarray` 对象“拥有”其数据，类似于 `vector`，而不是像 `mdspan` 那样作为对另一个容器拥有的数据的引用。它可能最早会在 C++26 中发布。

###### 注意

上述 `mdspan` 的代码示例可以在 C++20 中使用当前在 [P1673 GitHub 站点](https://github.com/kokkos/mdspan/blob/stable/include/experimental/__p0009_bits/mdspan.hpp) **{31}** 上可用的工作代码编译。安装和构建说明包含在存储库中，但需要了解的两个特定项是，首先，该代码目前位于命名空间 `std::experimental` 下，其次，由于多重索引的方括号运算符设置为 C++23，您可以在 C++20 及更早版本中用圆括号运算符替换它；即，

_

```
namespace stdex = std::experimental;
auto mds1 = stdex::mdspan{ v.data(), 3, 2 };

// Replace the square brackets here:
for (size_t i = 0; i < n_rows; ++i)
{
	for (size_t j = 0; j < n_cols; ++j)
		cout << mds1[i, j]			<< "\t";
}

// with round brackets:
for (size_t i = 0; i < n_rows; ++i)
{
	for (size_t j = 0; j < n_cols; ++j)
		cout << mds1(i, j) << "\t";
}
```

## BLAS 接口（P1673）

此提议是关于基于密集基本线性代数子程序（BLAS）的 C++ 标准库稠密线性代数接口的提案 {见引用 29}，也简称为“stdBLAS”。BLAS 库可以追溯到几十年前，最初是用 Fortran 编写的，但在 2000 年初发展为标准，现在有其他语言的实现，如 C（OpenBLAS）和 CUDA C++（NVIDIA），同时还有用于 Fortran 的 C 绑定。

Fortran BLAS 分发支持四种数值类型：`FLOAT`, `DOUBLE`, `COMPLEX`, 和 `DOUBLE COMPLEX`。其 C++ 等效类型为 `float`, `double`, `std::complex<float>`, 和 `std::complex<double>`。BLAS 库包含多种矩阵格式（标准、对称、上/下三角形）、矩阵和向量操作，如逐元素加法和矩阵/向量乘法。

使用此提议的实现，可以将相同的 C++ 代码库应用于任何包含 BLAS 功能的兼容库，前提是库供应商已经提供了接口。这将允许可移植的代码，独立于所使用的基础库。在这个阶段，尚不清楚哪些供应商最终会参与，但 NVIDIA 的实现是一个重要的进展，包括它们的 [HPC SDK](https://developer.nvidia.com/hpc-sdk) **{33}**。

值得注意的是，即使底层库提供额外的功能如矩阵分解、线性和最小二乘求解器等，stdBLAS 本身也只提供对特定矩阵操作的访问权限 — 下一步将进行讨论 — 。

BLAS 函数根据它们应用的矩阵和/或向量中包含的类型进行前置。例如，矩阵乘以向量的乘法函数的形式如下

```
xGEMV(.)
```

其中 `x` 可以是 `S`、`D`、`C` 或 `Z`，分别表示单精度 (`REAL` 在 Fortran 中)、双精度 (`DOUBLE`)、复数 (`COMPLEX`) 和双精度复数 (`DOUBLE COMPLEX`)。

在提议中的 C++ 等效函数 `matrix_vector_product` 取代了以前使用的 `mdspan` 对象表示矩阵和向量。例如，我们可以查看一个涉及 `double` 值的案例，使用 `m` 和 `n` 表示行数和列数。

```
std::vector<double> A_vec(m * n);
std::vector<double> x_vec(n);

// A_vec and x_vec are then populated with data...

std::vector<double> y_vec(n);		// empty vector

std::mdspan A{ A_vec.data(), std::extents{m, n} };
std::mdspan x{ x_vec.data(), std::extents{n} };
std::mdspan y{ y_vec.data(), std::extents{m} };
```

然后，进行乘法运算，向量积存储在 `y` 中：

```
std::linalg::matrix_vector_product(A, x, y); 	// y = A * x
```

下表提供了在金融编程中可能有用的 P1673 中的 BLAS 函数子集。假设 BLAS 函数为双精度，并且任何给定的矩阵/向量表达式均为适当的维度。

表 5-1\. 提议 P1673 中选择的 BLAS 函数

| BLAS 函数 | P1673 函数 | 描述 |
| --- | --- | --- |
| DSCAL | `scale` | 向量的标量乘法 |
| DCOPY | `copy` | 将一个向量复制到另一个向量 |
| DAXPY | `add` | 计算 <math alttext="alpha bold x plus bold y"><mrow><mi>α</mi> <mi>𝐱</mi> <mo>+</mo> <mi>𝐲</mi></mrow></math>，向量 <math alttext="bold x"><mi>𝐱</mi></math> 和 <math alttext="bold y"><mi>𝐲</mi></math>，标量 <math alttext="alpha"><mi>α</mi></math> |
| DDOT | `dot` | 两个向量的点积 |
| DNRM2 | `vector_norm2` | 向量的欧几里德范数 |
| DGEMV | `matrix_vector_product` | 计算 <math alttext="alpha bold upper A bold x plus beta bold y"><mrow><mi>α</mi> <mi>𝐀𝐱</mi> <mo>+</mo> <mi>β</mi> <mi>𝐲</mi></mrow></math>，矩阵 <math alttext="bold upper A"><mi>𝐀</mi></math>，向量 <math alttext="bold y"><mi>𝐲</mi></math>，标量 <math alttext="alpha"><mi>α</mi></math> 和 <math alttext="beta"><mi>β</mi></math> |
| DSYMV | `symmetric_matrix_vector_product` | 与 DGEMV (`matrix_vector_product`) 相同，但矩阵 <math alttext="bold upper A"><mi>𝐀</mi></math> 是对称的 |
| DGEMM | `matrix_product` | 计算<math alttext="alpha bold upper A bold upper B plus beta bold upper C"><mrow><mi>α</mi> <mi>𝐀𝐁</mi> <mo>+</mo> <mi>β</mi> <mi>𝐂</mi></mrow></math>，对于矩阵<math alttext="bold upper A"><mi>𝐀</mi></math>，<math alttext="bold upper B"><mi>𝐁</mi></math>和<math alttext="bold upper C"><mi>𝐂</mi></math>，以及标量<math alttext="alpha"><mi>α</mi></math>和<math alttext="beta"><mi>β</mi></math> |

## 线性代数（P1385）

本提案的作者特别认识到线性代数在金融建模中的重要性，以及在医学成像、机器学习和高性能计算等其他应用中的重要性。{引用 28}。

C++中线性代数库的初始技术要求在[之前的提案（P1166）](https://wg21.link/p1166) **{34}** 中概述，这里直接引用（斜体）：

*类型和函数的集合应该是在有限维空间中执行函数所需的最小集合。这包括*：

+   *矩阵模板*

+   *矩阵加法、减法和乘法的二元运算*

+   *标量乘法和矩阵除法的二元运算*

在此基础上，P1385 提案规定了两个主要目标，即库应该易于使用，并且“运行时计算性能接近于用户通过等效的函数调用序列获得的传统线性代数库（如 LAPACK、Blaze、Eigen 等）”。{引用 30}

从高层次来看，矩阵通常由*MathObj*类型泛化表示，而*engine*“是一个管理与*MathObj*实例相关资源的实现类型。”讨论仍在进行中，但“*MathObj*可能拥有存储其元素的内存，或者它可能使用一些非拥有视图类型，如`mdspan`，来操作由其他对象拥有的元素”。{同上}。*engine*对象被建议作为*MathObj*的私有成员，并且*MathObj*可以具有固定或动态维度。

在未来几年内，关于 P1385 线性代数库的形式应该会有更多细节浮出水面，但目前，这希望为金融软件开发人员提供一个初步的高层次预览，这应该是 C++中非常受欢迎的一个补充。

## 摘要（线性代数提案）

回顾我们在`valarray`中看到的结果，有了上述提案，同样方便的功能应该在 C++26 中得以实现，这一次将有严格、高效和一致的规范，避免了`valarray`困扰的问题。虽然`mdspan`作为非拥有引用与`valarray`不同，但它仍允许数组存储（如`vector`）通过指定行数和列数来适应矩阵代理。`submdspan`将承担类似于`valarray`切片的角色，但不会因对象复制而带来性能损失。P1673 将为包含 BLAS 函数的库提供一个公共接口，函数命名，例如`matrix_vector_product`，将比它们的 Fortran 等价物（如`DGEMV(.)`）更具表达性。而 P1385 中的`+`、`-`和`*`运算符将提供在自然数学格式中实现线性代数表达式的能力，这与我们在`valarray`中看到的结果类似。

这是一个令人兴奋的发展，将最终为 C++实现长期以来期待的基本矩阵计算提供高效可靠的方法。希望我们最终也能看到 P1673 BLAS 接口在 Eigen 等流行的开源库中的实现，但在撰写本文时，这仍是未知数。

# 章节总结

本章探讨了 C++中二维数组管理和线性代数的过去、现在和预期未来。`valarray` 自 C++98 起提供了类似矩阵的功能，对量化开发者来说应该有大量使用案例。不幸的是，尽管在 20 世纪 90 年代末 C++被宣传为计算金融的未来语言，但它从未得到委员会和社区的关注和支持。对于特定的编译器来说，它可能仍然是一个可行的选择，但由于其在标准库供应商中实现的不一致性，它会限制在不同平台上的代码复用。

在 2000 年代末，像 Eigen 和 Armadillo 这样的高质量开源线性代数库出现了，并受到金融量化 C++编程社区的好评。如今，这些库不仅包含 BLAS 标准中的许多功能，还包括在金融应用中经常使用的大量矩阵分解功能。

最终，ISO C++ 的未来看起来更加光明，因为三个提案——`mdspan`（P0009）、BLAS 接口（P1673）、线性代数库（P1385）——计划在接下来的三年内被纳入标准库中。这些功能可以说是早就应该有的，但它们将大步满足不仅来自金融行业，还包括其他计算密集型领域的需求。

# 参考文献

美国每日财政部利率曲线收益率 [*https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve&field_tdr_date_value_month=202211*](https://home.treasury.gov/resource-center/data-chart-center/interest-rates/TextView?type=daily_treasury_yield_curve&field_tdr_date_value_month=202211) _

[附加章节，_C++标准库，第 2 版 _](http://www.cppstdlib.com/cppstdlib_supplementary.pdf)

{2.5} Stroustrup，*C++之旅*（第 2 版，现在是第 3 版）

Quantstart 关于 Eigen 的文章 [*https://www.quantstart.com/articles/Eigen-Library-for-Matrix-Algebra-in-C/*](https://www.quantstart.com/articles/Eigen-Library-for-Matrix-Algebra-in-C/)

Gottschling _ 发现现代 C++

Bowie Owens，CppCon 2019，[*https://www.youtube.com/watch?v=4IUCBx5fIv0*](https://www.youtube.com/watch?v=4IUCBx5fIv0)

关于雅可比和双对角分解 SVD 类的更多信息：

[矩形矩阵的双边雅可比 SVD 分解](https://eigen.tuxfamily.org/dox-devel/classEigen_1_1JacobiSVD.html)

[双对角分解 SVD](https://eigen.tuxfamily.org/dox-devel/classEigen_1_1BDCSVD.html)

asciidoctor-latex -b html Ch10_v10_Split_LinearAlgebraOnly.adoc

{5} Rcpp 包：

[RcppEigen](https://cran.r-project.org/web/packages/RcppEigen/vignettes/RcppEigen-Introduction.pdf)

[RcppArmadillo](https://cran.r-project.org/web/packages/RcppArmadillo/vignettes/RcppArmadillo-intro.pdf)

[RcppBlaze3](https://github.com/ChingChuan-Chen/RcppBlaze3)

[Boost Headers，包括 uBLAS](https://www.rdocumentation.org/packages/BH/versions/1.78.0-0)
