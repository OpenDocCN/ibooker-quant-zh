# 第二章：C++ 的一些机制

几乎任何编程语言都会有一种数组结构来存储类似类型的集合。在 C++ 中，有一些选项可用于此目的，但是标准库 `vector` 容器是远远最常用的类型。在本章中，我们将看到一个 `vector` 如何方便地表示数学意义上的实数向量。我们还将介绍创建和使用 `vector` 以及其关键成员函数的基础知识，因为它与计数循环和 `while` 语句等迭代语句有很好的联系，这些也将在本章中介绍。

控制结构，包括迭代语句和条件语句。在 C++ 中，条件分支可以通过 `if` 语句实现，类似于其他语言，以及所谓的 `switch` 语句。与 `switch` 语句相关的一个很好的补充主题是枚举类型（enums），特别是在 C++11 中添加的更现代的 *enum classes* 。枚举类也非常适用于便利地输入和输出金融模型的数据。

最后，我们将总结可在 C++ 中使用的别名，并解释它们的重要性。这包括可以在代码中增加清晰度的类型别名，以代替更长并且有时更神秘的模板类型。引用和指针允许您访问和修改对象，而不需要对象复制的开销，尽管指针也有更广泛的用途。

# `vector` 容器

C++ 标准库中的 `vector` 容器是存储和管理类似类型的索引数组的首选选择。它特别适用于管理量化工作中广泛存在的实数向量，`double` 类型表示数值。

###### 注意

**历史注释**：`vector` 更具体地说是所谓的标准模板库（STL）的一部分。STL 是在 1980 年代和 90 年代，由研究员亚历山大·斯蒂帕诺夫独立开发的，与 Bjarne Stroupstrup 早期努力设计和生产 C++ 的努力是无关的。关于 STL 的被接纳和它被纳入 C++ 标准的历史是一个非常有趣的故事[[参见 Kalb/Azman]]，但总结是基于通用编程的 STL 在 1998 年被接纳为 C++ 的首个 ISO 标准版本。

作为一个通用容器，`std::vector` 可以保存来自于普通任意类型的元素，从普通数据（POD）类型如 `double` 和 `int`，到用户定义和库类的对象。

```
 #include <vector>
using std::vector;
//. . .
vector <double> x;		// Vector of real numbers
vector <BondTrade> bond_trades;	// Vector of user-defined BondTrade objects
```

要保存的类型在尖括号内指示。

###### 注意

尖括号表示 *模板参数*。模板是 C++ 实现通用编程的手段。这个主题将在第七章中进一步讨论。

## 设置和访问 `vector` 的元素

STL 的`vector`本质上封装和管理动态数组，这意味着在构造后可以向其中追加或从中移除元素。`vector`还支持随机访问，意味着可以访问和修改元素的索引。和 C++中的所有其他内容一样，`vector`是从零开始索引的，这意味着它的第一个位置的索引是 0，最后一个位置的索引是*n* - 1，如果它包含*n*个元素。

### 创建一个`vector`并使用它的索引

以下指令将创建一个持有三个实数的向量。

```
vector <double> v(3);	// Will hold three elements
```

`vector`可以像这里展示的那样逐个元素填充。注意索引从零开始而不是从一开始。

```
v[0] = 10.6;		// Set the first element (index 0) and assign to 10.6
v[1] = 58.63;		// Set the second element (index 1) and assign to 58.63
v[2] = 0.874;		// Set the first element (index 2) and assign to 0.874
```

方括号表示索引。我们还可以通过简单地将元素重新分配给一个新值来改变值；即，

```
v[1] = 13.68;
```

还可以使用 C++11 引入的*统一初始化*初始化向量。也称为*花括号初始化*，使用赋值操作符是可选的：

```
vector <double> w{9.8, 36.8, 91.3, 104.7}; // No assignment operator
vector <int> q = {4, 12, 15};	 // With assignment operator 
```

###### 注意

在 C++11 中增加统一初始化对语言产生了重大影响，不仅仅是初始化向量。它具有一些有趣和方便的特性，将在第四章中讨论。

### 成员函数

由于`vector`是一个类，它包含许多公共成员函数，包括`at`、`size`和`push_back`三个函数。

#### `at`函数

`at`函数本质上扮演与方括号操作符相同的角色，即为给定索引访问元素，或修改元素。

```
double val = v.at(2);	// val = 0.874
w.at(1) = val;			// 36.8 in w[1] is replaced with 0.874
```

使用方括号操作符和`at`函数的区别在于后者执行边界检查。两个例子是

+   尝试访问超过最大索引的元素；例如，

    ```
    double out_of_range = v.at(100);	// 2 is the max index for v
    ```

+   尝试使用负索引值

    ```
    w.at(-3) = 19.28;
    ```

在每种情况下，都会抛出一个异常，可以用于错误处理。否则，你可以认为`at`和`[.]`是相同的。

#### `size` 函数

此成员函数的名称使它的作用相当明显：它返回一个`vector`持有的元素数量：

```
auto num_elems_w = w.size();	// Returns 5
auto num_elems_q = q.size();	// Returns 3
```

你可能注意到这是我们第一次使用`auto`关键字。它自动推断从`size`函数返回的类型。我们将在以后的案例中看到`auto`有多么有用，但在这里，它帮助我们规避了`std::vector`容器的最大大小因编译器设置和使用平台而异的事实。该类型将是某种形式的无符号（非负）整数，有多种大小。

为了避免在这里陷入细节，我们不需要关注具体的无符号类型，因此我们可以大多数情况下只使用`auto`作为`size`成员函数的返回类型。

#### `push_back` 函数

此函数将元素追加到一个`vector`中；也就是说，新元素被“推到容器的后面”。

例如，我们可以在上述的`vector v`中追加一个新元素，比如说 47.44：

```
v.push_back(47.44);
```

现在，`v`包含四个值：10.6, 58.63, 0.84, 47.44，其中`v[3]`（第四个元素，使用 0 索引）等于新值。

我们也可以向空向量追加值。一开始，我们定义了

```
vector <double> x;		// x.size() = 0
```

现在，如果我们追加一个值，

```
x.push_back(3.08);	// x.size() = 1
```

`x`现在在其索引 0 位置包含值 3.08，并包含一个元素。这可以任意重复多次：

```
x.push_back(5.12);	// x.size() = 2
x.push_back(7.32);	// x.size() = 3
//. . . etc
```

关于`push_back`的讨论到此结束，还有一个需要注意的潜在问题，

假设我们创建了一个整数`vector`，其中包含三个元素：

`vector <int> ints(3);`

现在，每个元素将保持`int`类型的默认值：0。

如果我们然后应用`push_back`函数来追加，比如说，5：

`ints.push_back(5);`

这个值将作为第三个零后的*新元素*追加到向量中；即，向量现在包含四个元素。

`0 0 0 5`

要将值放入前三个位置中的任何一个，您需要显式使用索引；例如

```
ints[0] = 2;
ints.at(2) = 4;
```

## 关于 STL `vector`的总结说明

在上面的例子中，我们只使用了普通的数值类型`double`和`int`。当然，对于计算工作，实数向量是基础，但请记住 STL `vector`是通用的，可以容纳任何有效类型的元素，包括对象，而不仅仅是数值数据类型，这将在更高级的上下文中看到。

此外，正如前面提到的，在实际的生产级别编程中，输入来自于市场和产品数据以及用户输入的函数参数，而不是像前面示例中看到的硬编码的值。你可能会在测试函数中找到设置了固定数值的向量，但在生产代码中应避免使用它们。

标准库包含额外的 STL 容器，以及现代 C++编程的核心组成部分之一的大量 STL *算法*。这些将在第七章更详细地讨论，并且现在熟悉`vector`容器的基础知识将使这些材料在我们讨论它时更易于理解。

最后，重申一下，优先使用 STL `vector`而不是使用`new`和`delete`创建动态 C 风格数组。使用后者没有性能优势，并且内存管理完全封装在`vector`类中，解放开发人员免受内存泄漏风险。

# 枚举常量和类

*枚举常量*，简称为枚举，将文本映射到整数。在 C++11 之前，枚举是一种很好的方法，使我们这些凡人更容易理解整数代码，通过用（连续的）单词表示它们。对于机器来说，处理整数要比处理占用更多内存的`std::string`对象更有效率。最后，通过在引号字符和杂乱的字符串中避免拼写错误，也可以避免错误。

C++11 标准进一步改进了这一点，引入了*枚举类*。这些移除了使用普通枚举常量时可能发生的重叠整数值的歧义，同时保留了优势。

我们将讨论更倾向于现代枚举类而不是基于整数的枚举的动机。在接下来的部分中，我们将看到它们如何在条件语句中对我们有利。后来，在使用金融模型进行数据输入和输出时，它们将证明是有用的。

## 枚举常量

枚举允许我们在文本表示中传递标识符、分类、指标等，而在幕后，编译器将其识别为整数。

例如，我们可以创建一个名为`OptionType`的枚举，该枚举将指示简单交易系统中允许的期权交易类型，例如欧式、美式、百慕大和亚洲。声明`enum`类型；然后，在大括号内定义允许的类型，用逗号分隔。默认情况下，每个类型将被赋予一个从零开始逐一增加的整数值（记住，C++中的索引是从零开始的）。闭括号后必须跟着一个分号。在代码中，我们会这样写：

```
enum OptionType
{
    European,     	// default integer value = 0
    American,     	// default integer value = 1
    Bermudan,     	// default integer value = 2
    Asian	      	// default integer value = 3
};  
```

我们可以验证每种选项类型的对应整数值：

```
cout << " European = " << European << endl;
cout << " American = " << American << endl;
cout << " Bermudan = " << Bermudan << endl;
cout << " Asian = " << Asian << endl;
cout << endl;
```

检查输出，我们得到：

```
European
American
Bermudan
Asian
```

因此，我们可以看到程序将文本表示视为整数。请注意，这些文本标签*不*用引号括起来，因为它们最终代表的是整数类型，而不是字符串。

## 枚举可能的冲突

正如一开始讨论的那样，对于任何`enum`类型，默认的整数赋值从零开始，然后每个类型成员递增一。因此，两个来自两种不同类型的枚举常量可能在数字上相等。例如，假设我们定义了两种不同的`enum`类型，称为`Football`和`Baseball`，分别表示每种运动的防守位置。默认情况下，棒球位置从投手开始为 0，并逐一增加到列表中的每个位置。橄榄球位置也是如此，从防守后卫开始。在注释中提供了整数常量。

```
enum Baseball
{
	Pitcher,		// 0
	Catcher,		// 1
	First_Baseman,	// 2
	Second_Baseman,	// 3
	Third_Baseman,	// 4
	Shortstop,		// 5
	Left_Field,    // 6
	Center_Field,	// 7
	Right_Field	// 8
};
```

```
enum Football
{
	Defensive_Tackle,	// 0
	Edge_Rusher,		// 1
	Defensive_End,		// 2
	Linebacker,		// 3
	Cornerback,		// 4
	Strong_Safety,		// 5
	Weak_Safety		// 6
};
```

随后，我们可以比较`Defensive_End`和`First_Baseman`：

```
	if (Defensive_End == First_Baseman)
	{
		cout << " Defensive_End == First_Baseman is true" << endl;
	}
	else
	{
		cout << " Defensive_End != First_Baseman is true" << endl;
	}
```

我们的结果将是无意义的：

```
Defensive_End == First_Baseman is true
```

这是因为这两个位置都映射到整数值 2。

一个快速的修复，以及在 C++11 之前经常使用的方法，将是重新索引每组枚举；例如，

```
enum Baseball
{
	Pitcher = 100,	
	Catcher,		// 101
	First_Baseman,	// 102
	. . .
};
```

```
enum Football
{
	Defensive_Tackle = 200,
	Edge_Rusher,		// 201
	Defensive_End,		// 202
	. . .
};
```

现在，如果我们比较`Defensive_End`和`First_Baseman`，它们将不再相等，因为 202 ≠ 102。尽管如此，在大型代码库中可能有数百个枚举定义，因此可能会出现重叠并导致错误。C++11 引入的枚举类消除了这种风险。

## 枚举类

在 C++11 中引入了一种新的更健壮的方法，可以避免`enum`重叠，这种方法完全消除了整数表示。枚举的其他好处，如避免晦涩的数字代码和更大的字符串对象，仍然存在，但通过使用所谓的*enum class*避免了冲突。例如，我们可以在枚举类中定义债券和期货合同的类别，如下所示：

```
enum class Bond
{
	Government,
	Corporate,		      
	Municipal,
	Convertible
};
enum class Futures_Contract
{
	Gold,
	Silver,
	Oil,
	Natural_Gas,
	Wheat,
	Corn
	};
enum class Options_Contract
{
    European,     	
    American,     	
    Bermudan,     
    Asian	      
};
```

请注意，我们现在不再需要手动设置整数值以避免冲突，就像我们在常规枚举中所做的那样。

尝试比较两个不同枚举类的成员，例如 `Bond` 和 `Futures_Contract` 位置，现在将导致编译器错误。例如，以下内容甚至不会编译：

```
if(Bond::Corporate == Futures_Contract::Gold)
{
	// . . .
}
```

这对我们有利，因为在编译时捕获错误要比在运行时更好。现代最佳实践现在认为，我们应该优先使用枚举类而不是枚举常量[[参考 ISO 指南]]。

# 控制结构

控制结构包括两个类别：

+   条件分支，例如 `if` 语句

+   迭代控制循环中重复一组命令

在 C++中，与给定条件或序列相关的代码包含在由大括号定义的块中。类似于函数，块内声明的变量在块终止时将超出范围。这些结构也可以嵌套在彼此内部。

在前一节关于枚举和枚举类中，我们假设你已经熟悉了 `if` 条件的基础知识，但在这里，你可以阅读更全面的条件和迭代结构的回顾，这些将从现在开始广泛使用。两者都依赖于逻辑运算符来确定真或假条件，因此在控制结构的探索之前，有必要快速复习一下逻辑运算符和布尔类型。

C++ 的布尔类型，由 `bool` 表示，可以存储 `true` 或 `false` 的值。在幕后，`bool` 类型具有一个字节的大小，并且只能存储 `1` 表示 `true`，或 `0` 表示 `false`。请注意，`true` 和 `false` 没有放在引号中，因为它们不是字符类型。它们代表固定的整数值。

C++ 的相等和不等运算符将根据结果是真还是假返回 `bool` 类型。它们如下所示：

+   `<, >` 严格不等

+   `<=, >=` 包含不等

+   `==` 相等

+   `!=` 不等于

+   *And* 和 *Or* 操作分别用 `&&` 和 `||` 表示。

示例将在下一节关于条件分支中展示。

## 条件分支

C++ 支持大多数其他语言中常见的基于 `if` 的逻辑，以及 `switch`/`case` 语句，后者在特定情况下提供了对多个 `else if` 条件的更清晰替代方法。

### `if` 和相关条件

常见的条件分支语句

+   `if (condition) then (action)`

+   `if (condition) then (action), else (default action)`

+   `if (condition 1) then (action 1),`

+   `else if (condition 2) then (action 2)`

+   `...`

+   `else if (condition n) then (action n)`

+   `else (default action)`

由以下 C++语法表示。每个条件，无论是 `if`、`else if` 还是 `else`，在执行为 `true` 的条件时，都包含在单独的主体内，用大括号表示。

```
// Simple if
if (condition)
{
  // action
}
// if/else
if (condition)
{
  // action
}
else
{
  // default action
}
// if/else if.../else
if (condition 1)
{
  // action 1
}
else if (condition 2)
{
  // action 2
}
// ...
else if (condition n)
{
  // action n
}
else
{
  // default action
}
```

###### 提示

在包含`else if`的条件语句中，最佳实践是在最后包括一个默认的`else`块。如果没有，代码可能会编译无误并正常运行，但其执行可能会导致意外行为，在更大和更实际的代码库中可能会引起严重问题。

利用上述介绍的不等运算符，我们可以写一些关于`if`语句主题的简单示例的所有三种变体：

```
	int x = 1;
	int y = 2;
	int z = 3;

	// Simple if
	if (x > 0)
	{
		cout << x << " > 0" << endl;
	}
	// if/else
	if (x >= y)
	{
		cout << x << " >= " << y << endl;
	}
	else
	{
		cout << x << " is less than " << y << endl;
	}
	// if/else if.../else
	if (x == z)
	{
		cout << x << " == " << z << endl;
	}
	else if (x < z)
	{
		cout << x << " > " << z << endl;
	}
	else if (x > z)
	{
		cout << x << " < " << z << endl;
	}
	else
	{
		cout << "Default condition" << endl;
	}
```

###### 警告

由于浮点数数值表示和算术的特性，永远不应该测试两个`double`类型之间的精确相等性，也不应该将浮点类型与零完全相同地比较。这些情况将在另一个上下文中进一步讨论。

逻辑 AND 和 OR 运算符也可以在条件参数中使用。例如：

```
	#include <cmath>
	using std::abs;
	using std::exp;

	// Simple if
	if (x > 0 || y < 1)
	{
		cout << x << " > 0 OR " << y << " < 1 " << endl;
	}
	// if/else if.../else
	if (x > 0 && y < 1)
	{
		cout << x << " > 0 AND " << y << " < 1 " << endl;
	}
	else if (x <= 0 || y >= 1)
	{
		cout << x << " <= 0 OR " << y << " >= 1 " << endl;
	}
	else if (z <= 0 || (abs(x) > z && exp(y) < z))
	{
		cout << z << " <= 0 OR " << endl;
		cout << abs(x) << " > " << z << " AND " 
			      << exp(y) << " < " << z << endl;
	}
	else
	{
		cout << "Default condition" << endl;
	}
```

注意，在最后的`else if`条件中，我们将 AND 条件放在圆括号内，因为 OR 比 AND 具有更高的优先级。[[cppreference.com](https://en.cppreference.com/w/cpp/language/operator_precedence)]

最后，我们可以将逻辑条件赋给`bool`变量，并在`if`条件中使用，如下所示：

```
	bool cond1 = (x > 0 && y < 1);
	bool cond2 = (z <= 0 || (abs(x) > z && abs(y) < z));
	if (cond1)
	{
		cout << x << " > 0 AND " << y << " < 1 " << endl;
	}
	else if (!cond1)
	{
		cout << x << " <= 0 OR " << y << " >= 1 " << endl;
	}
	else if (cond2)
	{
		cout << z << " <= 0 OR " << endl;
		cout << abs(x) << " > " << z << " AND "
			<< abs(y) << " < " << z << endl;
	}
	else
	{
		cout << "Default condition" << endl;
	}
```

注意，布尔变量可以通过在其前面加上`!`运算符来取反，如上面第一个`else if`条件所示。

###### 警告

一个常见的陷阱是错误地使用`=`来测试相等性，而不是`==`。前者是赋值运算符，在这种情况下会导致意外行为。确保在测试相等性时使用`==`。

### 三元`if`语句

还有一种方便的单行快捷方式来表示简洁的`if-else`组合。语法如下：

*类型* `var = ` *逻辑条件* `? var_val_true`(如果`true`) : `var_val_false`(如果`false`);

在英语中，这意味着如果 _*逻辑条件*_ 为`true`，则将`var`的值分配给`var_val_true`；否则，分配给`var_val_false`。

代码示例应当更加清晰：

```
using std::sin;
using std::cos;
int j = 10;
int k = 20;
double theta = 3.14;
double result = j < k ? sin(theta) : cos(theta);
```

因此，在这个例子中，`result`将被赋予 sin(3.14)的值，即大约为零。

### `switch`/`case`语句

也称为`switch`语句，这种控制序列允许我们消除多个`else if`子句带来的一些混乱，但对于单个整数类型的*state*的分支的特定情况，或者替代地，映射到整数的枚举，或枚举类成员。

对于每一个可能的`case`，执行与匹配状态相对应的命令。与上述`else`条件类似，最后应该提供一个`default`动作，以捕捉不属于任何给定类别或处理无法通过的错误情况。

作为第一个示例，考虑一个假设整数条件表示选项类型的情况，在每个`cout`的位置，操作将是调用相应的定价模型。这将使我们的代码比使用多个`else if`语句更具可读性和可维护性。

```
void switch_statement(int x)
{
	switch (x)
	{
	case 0:
		cout << "European Option: Use Black-Scholes" << endl;
		break;
	case 1:    
		cout << "American Option: Use a lattice model" << endl;
		break;
	case 2:
		cout << "Bermudan Option: Use Longstaff-Schwartz Monte Carlo" << endl;
		break;
	case 3:
		cout << "Asian Option: Calculate average of the spot time series" << endl;
		break;
	default:
		cout << "Option type unknown" << endl;
		break;
	}
}
```

每个情况结束后，`break` 语句指示程序在执行特定状态的相应代码后退出 `switch` 语句。因此，如果 `x` 是 `1`，则会调用格点模型来定价美式期权，然后控制将传出 `switch` 语句的主体，而不是检查 `x` 是否为 `2`。

也有一些情况下，如果对多个状态执行相同的操作，则可能希望转到下一步。例如，在（美式）橄榄球中，如果进攻停滞，进攻方在第四次进攻失败时会将球踢出，不会得分。然而，如果球队得分，可能会踢出三分球，或者得分进攻有三种可能结果：

+   未中额外一分 -- 结果是六分

+   踢额外一分 -- 结果是七分

+   进行两分转换 -- 结果是八分

无论球队如何得分，都会将球踢给对手，因此对于情况 3、6、7 和 8，我们只需逐个遍历每种情况，直到踢球。这种准贝叶斯逻辑可以通过以下代码实现：

```
void switch_football(int x)
{
	switch (x)
	{
	case 0:		// Drive stalls
		cout << "Punt" << endl;
		break;
	case 3:		// Kick field goal
	case 6:		// Score touchdown; miss extra point(s)
	case 7:		// Kick extra point
	case 8:		// Score two-point conversion
		cout << "Kick off" << endl;
		break;
	default:
		cout << "Are you at a tennis match?" << endl;
		break;
	}
}
```

在 C++11 之前，用于期权定价 `case` 的 `switch` 的明显替代方案是用相应的枚举替换整数代码，从而使逻辑对人类更易理解（`cout` 消息保持不变）：

```
void switch_statement_enum(OptionType ot)
{
	switch (ot)
	{
	case European:		// = 0
		cout << "European Option: Use Black-Scholes" << endl;
		break;
	case American:		// = 1    
		. . .
	case Bermudan:		// = 2
		. . .
	case Asian:		// = 3
		. . .
	default:
		cout << "Option type unknown" << endl;
		break;
	}
}
```

然而，现代 ISO 指南现在更倾向于使用枚举类，原因如上所示，存在整数冲突。因此，我们只需将 `Options_Contract` 枚举类替换到前面的示例中即可得到：

```
void switch_enum_class_member(Options_Contract oc)
{
	switch (oc)
	{
	case Options_Contract::European:
		cout << "European Option: Use Black-Scholes" << endl;
		break;
	case Options_Contract::American:    
		. . .
	case Options_Contract::Bermudan:
		. . .
	case Options_Contract::Asian:
		. . .
	default:
		cout << "Option type unknown" << endl;
		break;
	}
}
```

## 迭代语句

在 C++ 中，有两个内置语言特性可以实现循环逻辑和迭代：

+   `while` 和 `do...while` 循环

+   `for` 循环（包括基于范围的 `for` 循环）

这些迭代命令将根据固定计数、逻辑条件为真或在 `vector` 中保存的一系列元素的范围上执行一段重复的代码块。

### `while` 和 `do...while` 循环

`while` 循环背后的基本工作流程是在逻辑表达式为 `true`（或者在 `false` 的情况下）时重复执行一段代码块。以下简单示例演示了一个简单的 `while` 循环，其中递增的整数值在其值严格小于某个固定最大值时输出到屏幕：

```
int i = 0;
int max = 10;
while (i < max)
{
	cout << i << ", ";
	++i;
}
```

我们的逻辑条件是 `i` 严格小于值 `max`。只要这个条件成立，`i` 的值将被递增

`do...while` 循环类似，只是通过将 `while` 条件放在末尾，可以保证循环至少执行一次。例如：

```
int i = 0;
int max = 10;
do 
{
	cout << i << ", ";
	++i;
} while (i < max);
```

注意，即使 `max` 设置为零或更小，仍会有一次通过 `do...while` 循环的行程，因为最大条件直到最后才会被检查。这是将其与更简单的 `while` 循环区分开的特点。

最终，我们将看到涉及更有趣的数学和金融应用的循环示例。

### `for`循环

这种结构是迭代计数范围的另一种形式。C++中使用的形式可以用以下伪代码示例概括：

```
for(initial expression executed only once;
exit condition executed at the beginning of every loop;
loop expression executed at the end of every loop)
{
DoSomeStuff;
}
```

这里的语法很重要，特别是在`for`参数中分隔三个表达式的分号。将其分解为 a、b 和 c 部分，我们会有：

```
for(a; b; c) 
```

每个部分通常依赖于某种形式的计数器，例如在`while`语句中看到的`int i`计数器；然而，现在我们将此索引移到`for`语句的参数中，这允许我们将增量从循环体中移除。（a）部分确定计数器的起始值，（b）指示停止的位置，（c）强制计数器如何增加或减少。

例如，我们可以使用`for`循环将上面的`while`示例重写如下：

```
int max = 10;
for(int i = 0; i < max; ++i)
{
	cout << i << ", ";		// we no longer need ++i in the body
}   
```

结果将与`while`循环示例中的结果完全相同。

1.  从技术上讲，前增量运算符和后增量运算符之间存在影响其他用途的差异，但在`for`中使用`++i`或`i++`将完全相同。通常更倾向于使用`++i`。

1.  同样，可以使用递减（`--`）减少索引值到某个最小值的`for`循环是合法的。

### `break`和`continue`

在迭代循环中，有时需要在达到最大或最小索引值之前或在指定的逻辑条件终止迭代之前退出循环。在计算金融中的一个典型例子是使用蒙特卡罗模拟定价障碍期权。模拟路径通常具有相同数量的时间步长；然而，在例如敲出障碍期权的情况下，如果基础资产价格上涨到障碍水平以上，我们需要退出循环。

通过应用与`switch`语句中相同的`break`命令来实现。这里展示了一个简单的示例，还演示了在`for`块中嵌套`if`条件：

```
int max = 10;
for (int i = 0; i < 100; ++i)
{
	cout << i << ", ";
	if (i > max)
	{
	    cout << "Passed i = " << max << "; I'm tired, so let's go home." 
	        << endl;
	    break;
	}
}
```

一旦`i`增加到 11，`if`语句为真，因此调用`break`命令，导致程序控制退出`for`循环。

还有`continue`关键字，可以用来继续循环的过程，但由于这已经是循环的默认行为，它的用处有限。

### 嵌套循环

除了在循环内部嵌套`if`条件外，在其他块中嵌套迭代块（无论是`for`还是`while`循环）也是可能的。在量化编程中，当实现常见的数值例程和金融模型时，很容易发现自己编写双甚至三重嵌套循环。然而，这种编码方式可能会迅速变得复杂且容易出错，因此需要采取特殊预防措施，并考虑稍后我们将讨论的替代方案。

### 基于范围的`for`循环

在 C++11 之前，通过使用索引作为计数器来遍历 `vector`。

```
vector<double> v;
// Populate the vector v and then use below:
for(unsigned i = 0; i < v.size(); ++i)
{
	// Do something with v[i] or v.at(i). . .
}
```

引入于 C++11 的基于范围的 `for` 循环使得代码更加函数化和优雅。而不是显式地使用 `vector` 的索引，基于范围的 `for` 循环只需说“对于 `v` 中的每个元素 `elem`，做某事”。

```
for(auto elem : v)
{
	// Use elem, rather than v[i] or v.at(i)
}
```

作为一个简单的例子，计算元素的总和：

```
double sum = 0.0;
for(auto elem : v)
{
	sum += elem;
}
```

我们完成了。不用担心索引错误，输入更少，代码更明显地表达了它正在做的事情。实际上，ISO 指南告诉我们要优先使用基于范围的 `for` 循环与 `vector` 对象，以及其他将在第七章讨论的 STL 容器。

# 别名

别名可以采用多种形式，第一种是便利性的形式，即“类型别名”，其中常用的参数化类型名称可以分配给更短且更具描述性的别名。

此外，“引用别名”有助于避免在将对象传递到函数时创建对象的副本，通常会在运行时显著加快速度。

指针也可以被视为别名，特别是用于类设计中表示活动对象（第四章中的 `this` 指针）。指针（现在还有智能指针）也可以用于分配持久性内存，但这是一个单独且更深入的讨论，将推迟到第六章。

引用和指针都可以帮助促进将在后续章节中介绍的“面向对象编程”的概念中的“继承”和“组合”。

## 类型别名

在数量代码中，`std::vector<double>` 对象因其显而易见的原因而无处不在。由于它使用如此频繁，通常会为其分配一个类型别名，例如 `RealVector`。这更好地表达了它在数学上的含义，而且我们不需要输入太多。

使用现代 C++，我们可以通过简单地定义别名 `RealVector` 来定义别名。

```
using RealVector = vector<double>;
```

然后，我们可以简单地写：

```
RealVector v = {3.19, 2.58, 1.06};
v.push_back(2.1);
v.push_back(1.7);
// etc...
```

只要在代码中使用之前定义了别名，那么就可以使用它了。

在 C++11 之前，不存在 `using` 命令的这种应用，因此类型别名是通过使用 `typedef` 命令来实现的；例如，

```
typedef vector<double> RealVector;
```

这也是有效的 C++，仍然存在于许多现代代码库中，但根据现代 ISO 指南，使用 `using` 形式更可取。详细原因超出本书范围，但要点是 `using` 可用于定义通用模板类型的别名（例如，不仅限于上述的 `double` 参数），而 `typedef` 不能。

## 引用

简单地说，引用为“变量”提供了别名，而不是类型。一旦定义了引用，访问或修改它与使用原始变量完全相同。通过在类型名称和引用名称之间放置一个和符号来创建引用，然后将其分配给原始变量。例如：

```
int original = 15;
int& ref = original;	// int& means "reference to an int"
```

在这一点上，如果在函数中访问或分配给另一个变量，`original`和`ref`都将返回 15。然而，重新将`original`重新分配为 12 也意味着现在`ref`返回 12。同样，重新分配`ref`会改变由`original`保存的值：

```
original = 12;			// ref now = 12	
ref = 4;				// original also now = 4
```

需要注意的是，引用必须在声明时同时进行分配。例如，

```
int& ozone;
```

将是无意义的，因为没有任何东西与其引用，代码将无法编译。此外，一旦定义了引用，就不能再将其重新分配给另一个变量直至其生命周期结束。

对于普通的数值类型，使用引用是微不足道的，但当将大对象传递到函数中时，它们变得很重要，以避免可能会使程序运行时性能严重下降的对象复制。

假设我们有一个包含 2000 个期权合约对象的`std::vector`？通过将其作为引用传递到函数中，可以访问原始对象本身而无需复制它。

然而，有一个需要注意的地方。请记住，如果修改了引用，原始变量也会被修改。因此，可以将引用参数设为`const`。然后，编译器将阻止对引用的任何修改尝试。

例如，这里有两个函数，它们将`std::vector<int>`对象作为引用参数。第一个函数返回元素的总和，因此不会尝试修改元素。然而，第二个函数尝试将每个元素重置为其两倍值然后返回元素的总和。这将导致编译器错误 - 这比运行时错误好得多 - 并阻止操作被执行：

```
// This is OK
using IntVector = std::vector<int>;
int sum_ints(const IntVector& v)
{
	int sum = 0;
	for (auto elem : v)
	{
		sum += elem;
	}

	return sum;
}
int sum_of_twice_the_ints(const IntVector& v)
{
	// Will not compile!  const prevents modification
	// of the elements in the vector v.

	int sum = 0;
	for (auto elem : v)
	{
		elem = 2 * elem;
		sum += elem;
	}

	return sum;
}
```

###### 注意

也可以将函数参数作为非`const`引用传递，以便在原地修改它。在这种情况下，一般会将返回类型设置为`void`，而不是返回修改后的变量。在现代 C++中这种做法已经很少有正当理由，因为*返回值优化*（*RVO*）使得对象默认以“原地返回”的方式而不是作为函数的副本返回。这已经成为编译器根据 ISO 标准（从 C++11 开始）的要求。

关于引用的最后一点与 Java 和 C#等托管语言相关，其中默认行为是通过非常量引用传递对象。在 C++中，默认是按值传递；因此，如果在 C++和托管语言之间切换，必须明确指示编译器期望一个引用的函数参数。这是程序员在切换时需要进行的调整。

## 指针

在 C++中，*指针*与引用有一些相似之处，它也可以是对另一个变量的别名，但它不像引用那样在其生命周期内永久绑定到变量。指针*指向*包含变量内容的内存地址，并且可以重定向到包含另一个变量的另一个内存地址。

不幸的是，这可能会让人困惑，因为变量的内存地址也是通过`&`运算符来表示的，除了另一个用于声明指针的运算符`*`。 一个简单的例子说明了这一点。 首先，声明并分配一个整数变量：

```
int x = 42;
```

接下来，使用`*`运算符声明一个指向整数的指针：

```
int* xp;
```

这意味着创建一个变量，它将是`int`类型的*指针*，但是还没有指向任何具体的东西; 这将在下一步中进行：

```
xp = &x;
```

在这种情况下，`&`运算符意味着`x`的地址。 `xp`现在指向包含`x`内容的内存地址，即 42。 请注意，此处的`&`用法与声明引用的含义不同。

通过应用`*`运算符来对`xp`*dereferencing*，现在我们可以访问该内存地址的内容。 如果我们把

```
std::cout << *xp << std::endl;
```

输出将是 42，就像我们对变量`x`应用了`std::cout`一样。 注意这里的`*`运算符在这里使用的是不同的上下文，访问内存的内容而不是声明指针。

我们还可以意味着我们可以更改`x`的值。 例如，放置

```
*xp = 25;
```

然后`*xp`和`x`都将返回值 25，而不是 42。

还可以*重新分配*指针`xp`到不同的内存地址; 这是无法使用引用进行的。 假设我们有一个不同的整数变量`y`，我们将`xp`重新分配到指向`y`的地址：

```
int y = 106;
xp = &y;
```

现在，`*xp`将返回 106 而不是 25，但`x`仍然等于 25。

###### 注意

`xp`，而不是`*xp`，将返回一个十六进制值，表示内存中包含`y`内容的第一个字节的地址。

与引用类似，指针也可以与对象一起使用。 如果我们有一个类`SomeClass`，其中有一个成员函数，例如`some_fcn`，那么我们可以定义一个指向`SomeClass`对象的指针：

```
SomeClass sc;
auto SomeClass* ptr_sc = &sc;
```

由于明显`ptr_sc`将指向一个`SomeClass`对象，我们可以使用`auto`关键字而不会使其上下文含糊不清。

假设`SomeClass`也有一个成员函数`some_fcn`。 通过解引用`ptr_sc`然后以通常的方式调用它：

```
(*ptr_sc).some_fcn();
```

然而更常见的是使用箭头表示的间接运算符：

```
ptr_sc->some_fcn();
```

这就是我们现在所需了解关于指针的一切。 更具体地说，这些示例发生在堆栈内存中，并且在定义它们的函数或控制块终止时会自动删除。 更高级的用法将稍后介绍。

###### 注意

指针还可以指向在堆内存中分配的内存，这使得值或对象可以在函数或控制块的范围之外的内存中保持持久。 这在涉及面向对象编程的某些情况下非常重要，并且需要额外注意。 此外，C++11 在标准库中引入了智能指针。 这些主题将在第五章中介绍。

# 函数和运算符重载

C++的一个关键特性，以及其他现代编程语言的特性，是实现同一函数名的不同版本，通过不同的输入参数集合进行区分。这被称为*函数重载*。作为量化程序员非常方便的相关特性是*运算符重载*，我们可以为特定类型定义操作，比如向量乘法。运算符重载并不像函数重载那样被多种语言支持；例如，在 Java 中就不支持。

## 函数重载

为了说明函数重载，让我们看一个`sum`函数的示例，它有两个版本，一个返回`double`类型，另一个返回`vector<double>`。第一个版本很简单，只是对两个实数求和。

```
#include <vector> 
// . . .
double sum(double x, double y)
{
	return x + y;
}
```

然而，第二个版本将接受两个`std::vector<double>`对象，并返回一个包含其元素和的向量。

```
std::vector<double> sum(const std::vector<double>& x, const std::vector<double>& y)
{
	// NOTE TO SELF: Can we do this with range-based for loops(?!)
	std::vector<double> vec_sum;
	if(x.size() == y.size())
	{
		for (int i = 0; i < x.size(); ++i)
		{
			vec_sum.push_back(x.at(i) + y.at(i));
		}	
	}
	return vec_sum;		// Empty if size of x and y do not match
}
```

正如我们所见，这两个函数执行两个不同的任务，并且根据参数类型具有不同的返回类型。

重载函数还可以根据*相同类型的参数数量*进行区分，并返回相同类型。例如（显然），我们可以定义一个接受三个实数的`sum`函数：

```
double sum(double x, double y, double z)
{
	return x + y + z;
}
```

现在，如果我们在`main()`函数中输入以下内容，

```
sum(5.31, 92.26);
sum(4.19, 41.9, 419.0);
```

将调用相应的重载函数。

## 运算符重载

C++为整数和浮点数提供了标准的数学运算符。标准库还为`std::string`类型提供了`+`运算符，用于连接它们。然而，例如对于`std::vector`，并未提供运算符。因此，如果我们想要计算两个向量的逐元素求和，或者计算点积，我们就得自己动手。

我们可以像上面显示的向量加法一样，使用`sum`重载，然后编写一个名为`dot_product`的新函数来进行向量乘法。然而，C++为我们提供了一个更自然的数学方法，即*运算符重载*。

对于向量求和，加法运算符取代了如下所示的`sum`重载。函数体保持不变：

```
std::vector<double> operator + (const std::vector<double>& x, const std::vector<double>& y)
{
	std::vector<double> add_vec;
	if (x.size() == y.size())
	{
		for (unsigned i = 0; i < x.size(); ++i)
		{
			add_vec.push_back(x.at(i) + y.at(i));
		}
	}
	return add_vec;		// Empty vector if x & y sizes not identical
}
```

同样地，对于返回标量(`double`)的点积，重载`*`运算符：

```
double operator * (const std::vector<double>& x, const std::vector<double>& y)
{
	double dot_prod = 0.0;
	if (x.size() == y.size())
	{
		for (int i = 0; i < x.size(); ++i)
		{
			dot_prod += (x[i] * y[i]);
		}
	}
	return dot_prod;	// Return 0.0 if size of x and y do not match		
}
```

然后，对于两个向量`x`和`y`，例如

```
std::vector<double> x = {1.1, 2.2, 3.3};
std::vector<double> y = {0.1, 0.2, 0.3};
```

重载的运算符将执行向量加法和乘法：

```
auto v_sum = x + y;		// ans: {1.2, 2.4, 3.6}
auto v_dot = x * y;		// ans: 1.54
```

对于`double`类型，编译器知道要应用语言提供的运算符

```
double s = 1.1 + 0.1;	// s = 1.2
double p = 2.2 * 0.2;	// p = 0.44
```

###### 注意

1.  对于同时迭代两个`vector`对象，在这个阶段我们需要回到索引的`for`循环。有更优雅的方法可以避免索引，但需要额外的背景，这将在第七章中介绍。

1.  对于`x.size() != y.size()`的错误条件，目前我们只是返回空向量作为向量和的结果，以及返回 0 作为点积的结果。在生产代码中，异常更为合适。

至于其他示例，如果我们编写一个`Matrix`类，我们还希望重载运算符`+`、`-`和`*`。对于`Date`类，我们可以定义`-`来返回两个日期之间的天数。因此，运算符重载在数学和金融编程中非常方便。我们将在以后的各种上下文中使用它。

# 总结

本章涵盖了一系列相当广泛的主题，从标准模板库（STL）中的`std::vector`容器类开始。`std::vector`在量化编程中无处不在，其（良好的）原因将在第七章详细讨论，同时还包括 STL 迭代器和算法，这些可以使 C++代码更加优雅、可靠和高效。然而，目前的目标是熟悉`std::vector`作为动态数组的实现。

别名有三种不同的形式：类型别名（`using`）、引用和指针。`using`使我们无需键入长类型名称，例如常用的`std::vector<double>`中的`RealVector`。在 C++中，引用主要用于将`const`引用对象作为函数参数传递，避免了可能降低性能的对象复制，并防止在函数内修改对象。指针除了作为纯粹的别名外，还有几个重要的应用场景，这些将在适当的时候介绍，同时还包括从 C++11 开始添加到标准库的智能指针。

函数重载对数学编程非常适合，对于如矩阵和向量这样在量化编程中无处不在的对象，运算符重载更是如此。这是另一个将在第四章的面向对象编程中进一步扩展的主题。

## 引用

[1] CppReference: `https://en.cppreference.com/w/cpp/language/operator_precedence`

[2] Stroustrup 4E（未直接引用）
