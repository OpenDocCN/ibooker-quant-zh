# 第二章。Python 基础设施

> 在建造房屋时，木材的选择是一个问题。
> 
> 重要的是，木匠的目标是携带能够良好切割的设备，并在有时间时磨刀。
> 
> 宫本武藏（*《五轮书》*）

对于新手来说，Python 部署可能看起来一切都不那么简单。对于可以选择安装的大量库和包，情况也是如此。首先，Python 不只有一种。Python 有许多不同的变体，如 CPython、Jython、IronPython 或 PyPy。然后还存在 Python 2.7 和 3.x 世界之间的分歧。本章重点介绍*CPython*，这是最流行的 Python 编程语言版本，以及版本 3.8。

即使专注于 CPython 3.8（以下简称“Python”），由于多种原因，部署也变得困难：

+   解释器（标准 CPython 安装）只带有所谓的*标准库*（例如，包括典型的数学函数）。

+   可选的 Python 包需要单独安装，而且有数百个这样的包。

+   编译（“构建”）这些非标准包可能会因为依赖关系和特定操作系统的要求而变得棘手。

+   处理这些依赖关系和长期版本一致性（维护）通常是乏味且耗时的。

+   特定包的更新和升级可能会导致需要重新编译大量其他包。

+   更改或替换一个包可能会在（许多）其他地方引起麻烦。

+   在稍后的某个时间点从一个 Python 版本迁移到另一个版本可能会放大所有前述问题。

幸运的是，有可用的工具和策略可以帮助解决 Python 部署问题。本章涵盖了以下几种技术类型，这些技术有助于 Python 部署：

包管理器

像[`pip`](https://oreil.ly/5vKCa)或[`conda`](https://oreil.ly/uTZRn)这样的包管理器帮助安装、更新和删除 Python 包。它们还有助于不同包的版本一致性。

虚拟环境管理器

像[`virtualenv`](https://oreil.ly/xMnlC)或`conda`这样的虚拟环境管理器可以让你并行管理多个 Python 安装（例如，在单台机器上同时安装 Python 2.7 和 3.8，或者测试最新的 Python 包的开发版本而不会有风险）^(1)。

容器

[Docker](http://docker.com)容器代表了包含运行特定软件所需的所有部件的完整文件系统，例如代码、运行时或系统工具。例如，您可以在运行 Mac OS 或 Windows 10 的机器上的 Docker 容器中运行一个带有 Python 3.8 安装和相应 Python 代码的 Ubuntu 20.04 操作系统。这样的容器化环境随后也可以在云中部署而无需进行任何重大更改。

云实例

部署用于金融应用的 Python 代码通常需要高可用性、安全性和性能。这些要求通常只能通过专业的计算和存储基础设施来满足，现在这些基础设施以非常有吸引力的条件提供，从相对小型到非常大型和强大的云实例都有。云实例（虚拟服务器）相对于长期租用的专用服务器的一个优势是，用户通常只需支付实际使用时间的费用。另一个优势是，这些云实例如果需要的话，可以在一两分钟内即可获得，这有助于敏捷开发和可伸缩性。

本章的结构如下。“包管理器 Conda”介绍了`conda`作为 Python 包管理器。“虚拟环境管理器 Conda”专注于`conda`在虚拟环境管理方面的功能。“使用 Docker 容器”简要介绍了作为容器化技术的 Docker，并侧重于构建一个基于 Ubuntu 的容器并安装 Python 3.8。“使用云实例”展示了如何在云中部署 Python 和[`Jupyter Lab`](https://oreil.ly/4LqUS)，这是一个强大的基于浏览器的 Python 开发和部署工具套件。

本章的目标是通过专业的基础设施，安装适用于 Python 的各种工具，以及数值分析和可视化包，来完成正确的 Python 安装。这一组合随后将作为后续章节中 Python 代码实施和部署的基础，无论是交互式金融分析代码还是脚本和模块形式的代码。

# [包管理器 Conda](https://oreil.ly/-Z_6H)

虽然`conda`可以单独安装，但更高效的方法是通过*Miniconda*，一个包含`conda`作为包和虚拟环境管理器的最小 Python 发行版。

## 安装 Miniconda

您可以在[Miniconda 页面](https://oreil.ly/-Z_6H)下载不同版本的 Miniconda。以下假设为 Python 3.8 的 64 位版本，适用于 Linux、Windows 和 Mac OS。本小节的主要示例是在基于 Ubuntu 的 Docker 容器中进行的会话，该容器通过`wget`下载 Linux 64 位安装程序，然后安装 Miniconda。如所示的代码应该可以（也许需要进行轻微修改）在任何其他基于 Linux 或 Mac OS 的机器上运行：^(2)

```py
$ docker run -ti -h pyalgo -p 11111:11111 ubuntu:latest /bin/bash

root@pyalgo:/# apt-get update; apt-get upgrade -y
...
root@pyalgo:/# apt-get install -y gcc wget
...
root@pyalgo:/# cd root
root@pyalgo:~# wget \
> https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
> -O miniconda.sh
...
HTTP request sent, awaiting response... 200 OK
Length: 93052469 (89M) [application/x-sh]
Saving to: 'miniconda.sh'

miniconda.sh              100%[============>]  88.74M  1.60MB/s    in 2m 15s

2020-08-25 11:01:54 (3.08 MB/s) - 'miniconda.sh' saved [93052469/93052469]

root@pyalgo:~# bash miniconda.sh

Welcome to Miniconda3 py38_4.8.3

In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>>
```

只需按下`ENTER`键即可开始安装过程。在查看许可协议后，通过回答`yes`来同意条款：

```py
...
Last updated February 25, 2020

Do you accept the license terms? [yes|no]
[no] >>> yes

Miniconda3 will now be installed into this location:
/root/miniconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/root/miniconda3] >>>
PREFIX=/root/miniconda3
Unpacking payload ...
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /root/miniconda3
...
  python             pkgs/main/linux-64::python-3.8.3-hcff3b4d_0
...
Preparing transaction: done
Executing transaction: done
installation finished.
```

当您同意许可条款并确认安装位置后，您应该再次回答`yes`来允许 Miniconda 将新的 Miniconda 安装位置添加到`PATH`环境变量中：

```py
Do you wish the installer to initialize Miniconda3
by running conda init? [yes|no]
[no] >>> yes
...
no change     /root/miniconda3/etc/profile.d/conda.csh
modified      /root/.bashrc

==> For changes to take effect, close and re-open your current shell. <==

If you'd prefer that conda's base environment not be activated on startup,
   set the auto_activate_base parameter to false:

conda config --set auto_activate_base false

Thank you for installing Miniconda3!
root@pyalgo:~#
```

之后，您可能希望更新`conda`，因为 Miniconda 安装程序通常不像`conda`本身那样定期更新：

```py
root@pyalgo:~# export PATH="/root/miniconda3/bin/:$PATH"
root@pyalgo:~# conda update -y conda
...
root@pyalgo:~# echo ". /root/miniconda3/etc/profile.d/conda.sh" >> ~/.bashrc
root@pyalgo:~# bash
(base) root@pyalgo:~#
```

在这个相对简单的安装过程之后，现在既有基本的 Python 安装，也有`conda`可用。基本的 Python 安装已经包含了一些不错的预装功能，比如[`SQLite3`](https://sqlite.org)数据库引擎。您可以尝试在*新的 Shell 实例*中启动 Python，或者在*附加相关路径*到相应环境变量后（如前面的例子中所做的）启动 Python：

```py
(base) root@pyalgo:~# python
Python 3.8.3 (default, May 19 2020, 18:47:26)
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print('Hello Python for Algorithmic Trading World.')
Hello Python for Algorithmic Trading World.
>>> exit()
(base) root@pyalgo:~#
```

## 使用 Conda 的基本操作

`conda`可以高效地处理，包括安装、更新和删除 Python 包。以下列表提供了主要功能的概述：

安装 Python x.x

`conda install python=x.x`

更新 Python

`conda update python`

安装一个包

`conda install $PACKAGE_NAME`

更新一个包

`conda update $PACKAGE_NAME`

移除一个包

`conda remove $PACKAGE_NAME`

更新 conda 本身

`conda update conda`

搜索包

`conda search $SEARCH_TERM`

列出已安装的包

`conda list`

有了这些功能，例如安装`NumPy`（作为所谓的*科学堆栈*中最重要的包之一）只需要一个命令。当在装有 Intel 处理器的机器上安装时，该过程会自动安装[Intel 数学核心库`mkl`](https://oreil.ly/Tca2C)，它不仅加速了`NumPy`在 Intel 机器上的数值运算，也为其他几个科学 Python 包提速：^(3)

```py
(base) root@pyalgo:~# conda install numpy
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /root/miniconda3

  added / updated specs:
    - numpy

The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    blas-1.0                   |              mkl           6 KB
    intel-openmp-2020.1        |              217         780 KB
    mkl-2020.1                 |              217       129.0 MB
    mkl-service-2.3.0          |   py38he904b0f_0          62 KB
    mkl_fft-1.1.0              |   py38h23d657b_0         150 KB
    mkl_random-1.1.1           |   py38h0573a6f_0         341 KB
    numpy-1.19.1               |   py38hbc911f0_0          21 KB
    numpy-base-1.19.1          |   py38hfa32c7d_0         4.2 MB
    ------------------------------------------------------------
                                           Total:       134.5 MB

The following NEW packages will be INSTALLED:

  blas               pkgs/main/linux-64::blas-1.0-mkl
  intel-openmp       pkgs/main/linux-64::intel-openmp-2020.1-217
  mkl                pkgs/main/linux-64::mkl-2020.1-217
  mkl-service        pkgs/main/linux-64::mkl-service-2.3.0-py38he904b0f_0
  mkl_fft            pkgs/main/linux-64::mkl_fft-1.1.0-py38h23d657b_0
  mkl_random         pkgs/main/linux-64::mkl_random-1.1.1-py38h0573a6f_0
  numpy              pkgs/main/linux-64::numpy-1.19.1-py38hbc911f0_0
  numpy-base         pkgs/main/linux-64::numpy-base-1.19.1-py38hfa32c7d_0

Proceed ([y]/n)? y

Downloading and Extracting Packages
numpy-base-1.19.1    | 4.2 MB    | ############################## | 100%
blas-1.0             | 6 KB      | ############################## | 100%
mkl_fft-1.1.0        | 150 KB    | ############################## | 100%
mkl-service-2.3.0    | 62 KB     | ############################## | 100%
numpy-1.19.1         | 21 KB     | ############################## | 100%
mkl-2020.1           | 129.0 MB  | ############################## | 100%
mkl_random-1.1.1     | 341 KB    | ############################## | 100%
intel-openmp-2020.1  | 780 KB    | ############################## | 100%
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
(base) root@pyalgo:~#
```

多个包也可以一次性安装。`-y`标志表示所有（可能的）问题都将回答为`yes`：

```py
(base) root@pyalgo:~# conda install -y ipython matplotlib pandas \
> pytables scikit-learn scipy
...
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /root/miniconda3

  added / updated specs:
    - ipython
    - matplotlib
    - pandas
    - pytables
    - scikit-learn
    - scipy

The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    backcall-0.2.0             |             py_0          15 KB
    ...
    zstd-1.4.5                 |       h9ceee32_0         619 KB
    ------------------------------------------------------------
                                           Total:       144.9 MB

The following NEW packages will be INSTALLED:

  backcall           pkgs/main/noarch::backcall-0.2.0-py_0
  blosc              pkgs/main/linux-64::blosc-1.20.0-hd408876_0
  ...
  zstd               pkgs/main/linux-64::zstd-1.4.5-h9ceee32_0

Downloading and Extracting Packages
glib-2.65.0          | 2.9 MB    | ############################## | 100%
...
snappy-1.1.8         | 40 KB     | ############################## | 100%
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
(base) root@pyalgo:~#
```

安装程序生成后，一些最重要的用于金融分析的库除了标准库外还可以使用：

[IPython](http://ipython.org)

改进的交互式 Python Shell

[matplotlib](http://matplotlib.org)

Python 的标准绘图库

[NumPy](http://numpy.org)

高效处理数值数组

[pandas](http://pandas.pydata.org)

管理表格数据，如金融时间序列数据

[PyTables](http://pytables.org)

Python 对[HDF5](http://hdfgroup.org)库的封装

[scikit-learn](http://scikit-learn.org)

用于机器学习及相关任务的包

[SciPy](http://scipy.org)

一组科学类和函数

这为一般数据分析和特别是金融分析提供了基本工具集。下一个例子使用`IPython`并使用`NumPy`生成一组伪随机数：

```py
(base) root@pyalgo:~# ipython
Python 3.8.3 (default, May 19 2020, 18:47:26)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.16.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]: import numpy as np

In [2]: np.random.seed(100)

In [3]: np.random.standard_normal((5, 4))
Out[3]:
array([[-1.74976547,  0.3426804 ,  1.1530358 , -0.25243604],
       [ 0.98132079,  0.51421884,  0.22117967, -1.07004333],
       [-0.18949583,  0.25500144, -0.45802699,  0.43516349],
       [-0.58359505,  0.81684707,  0.67272081, -0.10441114],
       [-0.53128038,  1.02973269, -0.43813562, -1.11831825]])

In [4]: exit
(base) root@pyalgo:~#
```

执行`conda list`可以显示已安装的包：

```py
(base) root@pyalgo:~# conda list
# packages in environment at /root/miniconda3:
#
# Name                    Version                   Build  Channel
_libgcc_mutex             0.1                        main
backcall                  0.2.0                      py_0
blas                      1.0                         mkl
blosc                     1.20.0               hd408876_0
...
zlib                      1.2.11               h7b6447c_3
zstd                      1.4.5                h9ceee32_0
(base) root@pyalgo:~#
```

如果不再需要某个包，可以使用`conda remove`高效移除：

```py
(base) root@pyalgo:~# conda remove matplotlib
Collecting package metadata (repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /root/miniconda3

  removed specs:
    - matplotlib

The following packages will be REMOVED:

The following packages will be REMOVED:

  cycler-0.10.0-py38_0
  ...
  tornado-6.0.4-py38h7b6447c_1

Proceed ([y]/n)? y

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
(base) root@pyalgo:~#
```

作为一个包管理器，`conda`已经非常有用。然而，只有在将虚拟环境管理添加到混合中时，其全部力量才会显现出来。

作为一个包管理器，`conda`使得安装、更新和移除 Python 包变得愉快。不需要自行处理构建和编译包，这有时可能会很棘手，因为一个包指定的依赖列表和不同操作系统上需要考虑的细节。

# Conda 作为虚拟环境管理器

安装了包括`conda`的 Miniconda 后，会根据所选择的 Miniconda 版本提供一个默认的 Python 安装。`conda`的虚拟环境管理功能允许用户在 Python 3.8 的默认安装基础上完全分离地安装 Python 2.7.x。为此，`conda`提供以下功能：

创建一个虚拟环境

`conda create --name $ENVIRONMENT_NAME`

激活一个环境

`conda activate $ENVIRONMENT_NAME`

停用一个环境

`conda deactivate $ENVIRONMENT_NAME`

移除一个环境

`conda env remove --name $ENVIRONMENT_NAME`

导出到环境文件

`conda env export > $FILE_NAME`

从文件创建一个环境

`conda env create -f $FILE_NAME`

列出所有环境

`conda info --envs`

作为一个简单的示例，接下来的示例代码创建了一个名为`py27`的环境，安装了`IPython`，并执行了一行 Python 2.7.x 的代码。尽管 Python 2.7 的支持已经结束，但这个示例说明了如何轻松执行和测试遗留的 Python 2.7 代码：

```py
(base) root@pyalgo:~# conda create --name py27 python=2.7
Collecting package metadata (current_repodata.json): done
Solving environment: failed with repodata from current_repodata.json,
will retry with next repodata source.
Collecting package metadata (repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /root/miniconda3/envs/py27

  added / updated specs:
    - python=2.7

The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    certifi-2019.11.28         |           py27_0         153 KB
    pip-19.3.1                 |           py27_0         1.7 MB
    python-2.7.18              |       h15b4118_1         9.9 MB
    setuptools-44.0.0          |           py27_0         512 KB
    wheel-0.33.6               |           py27_0          42 KB
    ------------------------------------------------------------
                                           Total:        12.2 MB

The following NEW packages will be INSTALLED:

  _libgcc_mutex      pkgs/main/linux-64::_libgcc_mutex-0.1-main
  ca-certificates    pkgs/main/linux-64::ca-certificates-2020.6.24-0
  ...
  zlib               pkgs/main/linux-64::zlib-1.2.11-h7b6447c_3

Proceed ([y]/n)? y

Downloading and Extracting Packages
certifi-2019.11.28   | 153 KB    | ############################### | 100%
python-2.7.18        | 9.9 MB    | ############################### | 100%
pip-19.3.1           | 1.7 MB    | ############################### | 100%
setuptools-44.0.0    | 512 KB    | ############################### | 100%
wheel-0.33.6         | 42 KB     | ############################### | 100%
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate py27
#
# To deactivate an active environment, use
#
#     $ conda deactivate

(base) root@pyalgo:~#
```

注意环境激活后提示符的变化，包含`(py27)`：

```py
(base) root@pyalgo:~# conda activate py27
(py27) root@pyalgo:~# pip install ipython
DEPRECATION: Python 2.7 will reach the end of its life on January 1st, 2020.
...
Executing transaction: done
(py27) root@pyalgo:~#
```

最后，这使得用户可以使用`IPython`并使用 Python 2.7 语法：

```py
(py27) root@pyalgo:~# ipython
Python 2.7.18 |Anaconda, Inc.| (default, Apr 23 2020, 22:42:48)
Type "copyright", "credits" or "license" for more information.

IPython 5.10.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: print "Hello Python for Algorithmic Trading World."
Hello Python for Algorithmic Trading World.

In [2]: exit
(py27) root@pyalgo:~#
```

正如这个示例所展示的，`conda`作为一个虚拟环境管理器，允许用户在同一台机器上安装不同的 Python 版本。它还允许用户安装不同版本的特定包。默认的 Python 安装不会受到这样的操作的影响，也不会影响同一机器上可能存在的其他环境。可以通过`conda info --envs`命令显示所有可用的环境：

```py
(py27) root@pyalgo:~# conda env list
# conda environments:
#
base                     /root/miniconda3
py27                  *  /root/miniconda3/envs/py27

(py27) root@pyalgo:~#
```

有时需要与他人共享环境信息或在多台机器上使用环境信息。为此，可以通过`conda env export`将已安装的包列表导出到文件中。然而，默认情况下，这仅适用于相同的操作系统，因为生成的`yaml`文件中指定了构建版本。可以通过`--no-builds`标志删除它们，只指定包的版本：

```py
(py27) root@pyalgo:~# conda deactivate
(base) root@pyalgo:~# conda env export --no-builds > base.yml
(base) root@pyalgo:~# cat base.yml
name: base
channels:
  - defaults
dependencies:
  - _libgcc_mutex=0.1
  - backcall=0.2.0
  - blas=1.0
  - blosc=1.20.0
  ...
  - zlib=1.2.11
  - zstd=1.4.5
prefix: /root/miniconda3
(base) root@pyalgo:~#
```

通常，虚拟环境仅仅是一个特定的（子）文件夹结构，用于进行一些快速测试。在这种情况下，环境可以通过`conda env remove`命令轻松地（在停用后）移除：

```py
(base) root@pyalgo:~# conda env remove -n py27

Remove all packages in environment /root/miniconda3/envs/py27:

(base) root@pyalgo:~#
```

这就完成了`conda`作为虚拟环境管理器的概述。

`conda` 不仅帮助管理软件包，还是 Python 的虚拟环境管理器。它简化了创建不同 Python 环境的过程，允许在同一台机器上拥有多个 Python 版本和可选包，而彼此之间不会相互影响。`conda` 还允许将环境信息导出，以便在多台机器上轻松复制或与他人分享。

# 使用 Docker 容器

Docker 容器已经在 IT 领域掀起了风暴（参见[Docker](http://docker.com)）。尽管技术还相对年轻，但已经确立了作为几乎任何软件应用高效开发和部署的标准之一。

对于我们的目的，可以将 Docker 容器简单地理解为一个独立的文件系统，其中包括操作系统（例如，用于服务器的 Ubuntu 20.04 LTS）、一个（Python）运行时、额外的系统和开发工具，以及根据需要的其他（Python）库和包。这样的 Docker 容器可以在本地运行的 Windows 10 专业版 64 位机器上，也可以在带有 Linux 操作系统的云实例上运行。

本节详细介绍了 Docker 容器的精彩细节。它简要说明了 Docker 技术在 Python 部署背景下的应用[⁵]。

## Docker 镜像和容器

在进入说明之前，谈论 Docker 时需要区分两个基本术语。第一个是*Docker 镜像*，可以类比为 Python 类。第二个是*Docker 容器*，可以类比为相应 Python 类的实例。

在更技术层面上，您将在[Docker 术语表](https://oreil.ly/NNUiB)中找到*Docker 镜像*的以下定义：

> Docker 镜像是容器的基础。镜像是一组有序的根文件系统更改及其在容器运行时使用的执行参数。镜像通常包含一组层式文件系统的联合，依次堆叠在一起。镜像没有状态，永远不会改变。

类似地，在[Docker 术语表](https://oreil.ly/NNUiB)中，您将找到*Docker 容器*的以下定义，这使得它与 Python 类和这些类的实例之间的类比更加透明：

> 容器是 Docker 镜像的运行时实例。
> 
> Docker 容器包括
> 
> +   一个 Docker 镜像
> +   
> +   一个执行环境
> +   
> +   一组标准指令
> +   
> 这个概念借鉴了集装箱运输，它定义了全球货物运输的标准。Docker 定义了软件运输的标准。

根据操作系统的不同，安装 Docker 也有所不同。因此，本节不涉及各自的详细信息。有关更多信息和进一步链接，请参阅[获取 Docker 页面](https://oreil.ly/hGgxs)。

## 构建 Ubuntu 和 Python Docker 镜像

这个小节展示了基于最新版本的 Ubuntu 构建 Docker 镜像的过程，包括 Miniconda 和一些重要的 Python 包。此外，它通过更新 Linux 软件包索引、根据需要升级软件包并安装某些额外的系统工具来进行一些 Linux 基础工作。为此，需要两个脚本。一个是在 Linux 级别上执行所有工作的 `Bash` 脚本。另一个是所谓的 *Dockerfile*，控制镜像本身的构建过程。

示例 2-1 中的 `Bash` 脚本负责安装，它包括三个主要部分。第一部分处理 Linux 的基础工作。第二部分安装 Miniconda，而第三部分安装可选的 Python 包。还有更详细的内联注释：

##### 示例 2-1\. 安装 Python 和可选包的脚本

```py
#!/bin/bash
#
# Script to Install
# Linux System Tools and
# Basic Python Components
#
# Python for Algorithmic Trading
# (c) Dr. Yves J. Hilpisch
# The Python Quants GmbH
#
# GENERAL LINUX
apt-get update  # updates the package index cache
apt-get upgrade -y  # updates packages
# installs system tools
apt-get install -y bzip2 gcc git  # system tools
apt-get install -y htop screen vim wget  # system tools
apt-get upgrade -y bash  # upgrades bash if necessary
apt-get clean  # cleans up the package index cache

# INSTALL MINICONDA
# downloads Miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O \
  Miniconda.sh
bash Miniconda.sh -b  # installs it
rm -rf Miniconda.sh  # removes the installer
export PATH="/root/miniconda3/bin:$PATH"  # prepends the new path

# INSTALL PYTHON LIBRARIES
conda install -y pandas  # installs pandas
conda install -y ipython  # installs IPython shell

# CUSTOMIZATION
cd /root/
wget http://hilpisch.com/.vimrc  # Vim configuration
```

示例 2-2 中的 `Dockerfile` 使用 示例 2-1 中的 `Bash` 脚本来构建新的 Docker 镜像。它还在内联中注释了其主要部分：

##### 示例 2-2\. Dockerfile 用于构建镜像

```py
#
# Building a Docker Image with
# the Latest Ubuntu Version and
# Basic Python Install
#
# Python for Algorithmic Trading
# (c) Dr. Yves J. Hilpisch
# The Python Quants GmbH
#

# latest Ubuntu version
FROM ubuntu:latest

# information about maintainer
MAINTAINER yves

# add the bash script
ADD install.sh /
# change rights for the script
RUN chmod u+x /install.sh
# run the bash script
RUN /install.sh
# prepend the new path
ENV PATH /root/miniconda3/bin:$PATH

# execute IPython when container is run
CMD ["ipython"]
```

如果这两个文件位于同一个文件夹中，并且已安装 Docker，则构建新的 Docker 镜像非常简单。在这里，标签 `pyalgo:basic` 用于这个镜像的引用。例如，在基于它运行容器时需要这个标签：

```py
(base) pro:Docker yves$ docker build -t pyalgo:basic .
Sending build context to Docker daemon  4.096kB
Step 1/7 : FROM ubuntu:latest
 ---> 4e2eef94cd6b
Step 2/7 : MAINTAINER yves
 ---> Running in 859db5550d82
Removing intermediate container 859db5550d82
 ---> 40adf11b689f
Step 3/7 : ADD install.sh /
 ---> 34cd9dc267e0
Step 4/7 : RUN chmod u+x /install.sh
 ---> Running in 08ce2f46541b
Removing intermediate container 08ce2f46541b
 ---> 88c0adc82cb0
Step 5/7 : RUN /install.sh
 ---> Running in 112e70510c5b
...
Removing intermediate container 112e70510c5b
 ---> 314dc8ec5b48
Step 6/7 : ENV PATH /root/miniconda3/bin:$PATH
 ---> Running in 82497aea20bd
Removing intermediate container 82497aea20bd
 ---> 5364f494f4b4
Step 7/7 : CMD ["ipython"]
 ---> Running in ff434d5a3c1b
Removing intermediate container ff434d5a3c1b
 ---> a0bb86daf9ad
Successfully built a0bb86daf9ad
Successfully tagged pyalgo:basic
(base) pro:Docker yves$
```

现有的 Docker 镜像可以通过 `docker images` 命令列出。新镜像应该位于列表的顶部：

```py
(base) pro:Docker yves$ docker images
REPOSITORY         TAG              IMAGE ID          CREATED             SIZE
pyalgo             basic            a0bb86daf9ad      2 minutes ago       1.79GB
ubuntu             latest           4e2eef94cd6b      5 days ago          73.9MB
(base) pro:Docker yves$
```

成功构建了 `pyalgo:basic` 镜像后，可以使用 `docker run` 命令来运行相应的 Docker 容器。参数组合 `-ti` 对于在 Docker 容器内运行交互式进程（比如 `IPython` 的 shell 进程）是必需的（参见 [Docker Run 参考页面](https://oreil.ly/s0_hn)）：

```py
(base) pro:Docker yves$ docker run -ti pyalgo:basic
Python 3.8.3 (default, May 19 2020, 18:47:26)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.16.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]: import numpy as np

In [2]: np.random.seed(100)

In [3]: a = np.random.standard_normal((5, 3))

In [4]: import pandas as pd

In [5]: df = pd.DataFrame(a, columns=['a', 'b', 'c'])

In [6]: df
Out[6]:
          a         b         c
0 -1.749765  0.342680  1.153036
1 -0.252436  0.981321  0.514219
2  0.221180 -1.070043 -0.189496
3  0.255001 -0.458027  0.435163
4 -0.583595  0.816847  0.672721
```

退出 `IPython` 将同时退出容器，因为它是容器内唯一运行的应用程序。但是，您可以通过以下方式从容器分离：

```py
Ctrl+p --> Ctrl+q
```

在从容器分离后，`docker ps` 命令显示运行中的容器（以及可能的其他当前运行容器）：

```py
(base) pro:Docker yves$ docker ps
CONTAINER ID  IMAGE         COMMAND     CREATED       ...    NAMES
e93c4cbd8ea8  pyalgo:basic  "ipython"   About a minute ago   jolly_rubin
(base) pro:Docker yves$
```

通过 `docker attach $CONTAINER_ID` 来附加到 Docker 容器。请注意，`CONTAINER ID` 的几个字母就足够了：

```py
(base) pro:Docker yves$ docker attach e93c
In [7]: df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5 entries, 0 to 4
Data columns (total 3 columns):
 #   Column  Non-Null Count  Dtype
---  ------  --------------  -----
 0   a       5 non-null      float64
 1   b       5 non-null      float64
 2   c       5 non-null      float64
dtypes: float64(3)
memory usage: 248.0 bytes
```

`exit` 命令终止 `IPython` 并因此停止 Docker 容器。可以通过 `docker rm` 命令移除它：

```py
In [8]: exit
(base) pro:Docker yves$ docker rm e93c
e93c
(base) pro:Docker yves$
```

类似地，如果不再需要，可以通过 `docker rmi` 移除 Docker 镜像 `pyalgo:basic`。虽然容器比较轻量级，但单个镜像可能会占用大量存储空间。对于 `pyalgo:basic` 镜像，其大小接近 2 GB。因此，您可能希望定期清理 Docker 镜像列表：

```py
(base) pro:Docker yves$ docker rmi a0bb86
Untagged: pyalgo:basic
Deleted: sha256:a0bb86daf9adfd0ddf65312ce6c1b068100448152f2ced5d0b9b5adef5788d88
...
Deleted: sha256:40adf11b689fc778297c36d4b232c59fedda8c631b4271672cc86f505710502d
(base) pro:Docker yves$
```

当然，在某些应用场景中，关于 Docker 容器及其优势还有很多可以说的。在本书中，它们提供了一种现代化的方法来部署 Python，以在完全分离的（容器化）环境中进行 Python 开发，并为算法交易提供代码发布的途径。

如果您尚未使用 Docker 容器，应考虑开始使用它们。在处理 Python 部署和开发工作时，它们提供了许多好处，不仅在本地工作时，特别是在与远程云实例和服务器部署算法交易代码时。

# 使用云实例

本节展示了如何在[DigitalOcean](http://digitalocean.com)云实例上设置完整的 Python 基础设施。市面上有许多其他云提供商，其中以[Amazon Web Services](http://aws.amazon.com)（AWS）为主要提供商。然而，DigitalOcean 以其简易性和较低的小型云实例费率而闻名，其称之为*Droplet*。最小的 Droplet，通常足以进行探索和开发，每月只需 5 美元或每小时 0.007 美元。按小时计费，因此可以（例如）轻松地启动一个 Droplet 两小时，销毁它，并只收取 0.014 美元的费用。^(7)

本节的目标是在 DigitalOcean 上设置一个 Droplet，该 Droplet 安装了 Python 3.8 并包含通常所需的包（如`NumPy`和`pandas`），并结合密码保护和安全套接层（SSL）加密的[Jupyter Lab](http://jupyter.org)服务器安装。^(8) 作为一个基于 Web 的工具套件，`Jupyter Lab`提供了几个可以通过常规浏览器使用的工具：

Jupyter Notebook

这是最受欢迎的（如果不是*最受欢迎的*）基于浏览器的交互式开发环境，具有不同语言内核的选择，如 Python、R 和 Julia。

Python 控制台

这是基于`IPython`的控制台，其图形用户界面与标准的基于终端的实现外观和感觉不同。

终端

这是一个通过浏览器访问的系统 shell 实现，不仅允许进行所有典型的系统管理任务，还可以使用诸如[`Vim`](http://vim.org/download)进行代码编辑或[`git`](https://git-scm.com/)进行版本控制等有用工具。

编辑器

另一个重要工具是基于浏览器的文本文件编辑器，支持许多不同的编程语言和文件类型的语法高亮显示，以及典型的文本/代码编辑功能。

文件管理器

`Jupyter Lab`还提供了一个功能齐全的文件管理器，可以进行典型的文件操作，如上传、下载和重命名。

在 Droplet 上安装`Jupyter Lab`允许通过浏览器进行 Python 开发和部署，避免通过安全外壳（SSH）访问云实例的需要。

要完成本节的目标，需要几个脚本：

服务器设置脚本

此脚本编排所有必要的步骤，例如复制其他文件到 Droplet 并在 Droplet 上运行它们。

Python 和`Jupyter`安装脚本

此脚本安装 Python、额外的包、`Jupyter Lab`并启动`Jupyter Lab`服务器。

Jupyter Notebook 配置文件

此文件用于配置`Jupyter Lab`服务器，例如关于密码保护的设置。

RSA 公钥和私钥文件

这两个文件是与`Jupyter Lab`服务器通信的 SSL 加密所必需的。

下一节反向操作列出这些文件，因为虽然设置脚本首先执行，但其他文件需要事先创建。

## RSA 公钥和私钥

为了通过任意浏览器与`Jupyter Lab`服务器建立安全连接，需要包含 RSA 公钥和私钥的 SSL 证书（参见[RSA 维基百科页面](https://oreil.ly/8UG1K)）。通常，人们期望这样的证书来自所谓的证书颁发机构（CA）。然而，对于本书的目的，自动生成的证书“足够好”。一个流行的工具用于生成 RSA 密钥对是[`OpenSSL`](http://openssl.org)。接下来的简短交互会话生成适用于`Jupyter Lab`服务器的证书（参见[Jupyter Notebook 文档](https://oreil.ly/YxxaF)）：

```py
(base) pro:cloud yves$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
> -keyout mykey.key -out mycert.pem
Generating a RSA private key
.......+++++
.....+++++
+++++
writing new private key to 'mykey.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank.
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:DE
State or Province Name (full name) [Some-State]:Saarland
Locality Name (e.g., city) []:Voelklingen
Organization Name (eg, company) [Internet Widgits Pty Ltd]:TPQ GmbH
Organizational Unit Name (e.g., section) []:Algorithmic Trading
Common Name (e.g., server FQDN or YOUR name) []:Jupyter Lab
Email Address []:pyalgo@tpq.io
(base) pro:cloud yves$
```

需要将两个文件`mykey.key`和`mycert.pem`复制到 Droplet，并在`Jupyter Notebook`配置文件中引用这些文件。接下来会介绍这个文件。

## Jupyter Notebook 配置文件

可以根据[Jupyter Notebook 文档](https://oreil.ly/YxxaF)安全部署公共`Jupyter Lab`服务器。其中，`Jupyter Lab`应该设置密码保护。为此，`notebook.auth`子包中有一个名为`passwd()`的函数可以生成密码哈希码。以下代码生成一个以`jupyter`为密码的密码哈希码：

```py
In [1]: from notebook.auth import passwd

In [2]: passwd('jupyter')
Out[2]: 'sha1:da3a3dfc0445:052235bb76e56450b38d27e41a85a136c3bf9cd7'

In [3]: exit
```

此哈希码需要放置在`Jupyter Notebook`配置文件中，如示例 2-3 所示。配置文件假定 RSA 密钥文件已复制到 Droplet 的`/root/.jupyter/`文件夹中。

##### 示例 2-3. Jupyter Notebook 配置文件

```py
#
# Jupyter Notebook Configuration File
#
# Python for Algorithmic Trading
# (c) Dr. Yves J. Hilpisch
# The Python Quants GmbH
#
# SSL ENCRYPTION
# replace the following file names (and files used) by your choice/files
c.NotebookApp.certfile = u'/root/.jupyter/mycert.pem'
c.NotebookApp.keyfile = u'/root/.jupyter/mykey.key'

# IP ADDRESS AND PORT
# set ip to '*' to bind on all IP addresses of the cloud instance
c.NotebookApp.ip = '0.0.0.0'
# it is a good idea to set a known, fixed default port for server access
c.NotebookApp.port = 8888

# PASSWORD PROTECTION
# here: 'jupyter' as password
# replace the hash code with the one for your password
c.NotebookApp.password = \
	'sha1:da3a3dfc0445:052235bb76e56450b38d27e41a85a136c3bf9cd7'

# NO BROWSER OPTION
# prevent Jupyter from trying to open a browser
c.NotebookApp.open_browser = False

# ROOT ACCESS
# allow Jupyter to run from root user
c.NotebookApp.allow_root = True
```

下一步是确保 Python 和`Jupyter Lab`在 Droplet 上安装。

在云中部署`Jupyter Lab`会导致一些安全问题，因为它是通过 Web 浏览器访问的全功能开发环境。因此，使用`Jupyter Lab`服务器默认提供的安全措施至关重要，如密码保护和 SSL 加密。但这只是开始，根据在云实例上具体执行的任务，可能建议采取进一步的安全措施。

## Python 和 Jupyter Lab 的安装脚本

安装 Python 和`Jupyter Lab`的 Bash 脚本类似于在 Docker 容器中通过 Miniconda 安装 Python 的“使用 Docker 容器”部分中提供的脚本。然而，在示例 2-4 中的脚本还需要启动`Jupyter Lab`服务器。所有主要部分和代码行都在内联中有注释。

##### 示例 2-4\. 安装 Python 并运行`Jupyter Notebook`服务器的 Bash 脚本

```py
#!/bin/bash
#
# Script to Install
# Linux System Tools and Basic Python Components
# as well as to
# Start Jupyter Lab Server
#
# Python for Algorithmic Trading
# (c) Dr. Yves J. Hilpisch
# The Python Quants GmbH
#
# GENERAL LINUX
apt-get update  # updates the package index cache
apt-get upgrade -y  # updates packages
# install system tools
apt-get install -y build-essential git  # system tools
apt-get install -y screen htop vim wget  # system tools
apt-get upgrade -y bash  # upgrades bash if necessary
apt-get clean  # cleans up the package index cache

# INSTALLING MINICONDA
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
		-O Miniconda.sh
bash Miniconda.sh -b  # installs Miniconda
rm -rf Miniconda.sh  # removes the installer
# prepends the new path for current session
export PATH="/root/miniconda3/bin:$PATH"
# prepends the new path in the shell configuration
cat >> ~/.profile <<EOF
export PATH="/root/miniconda3/bin:$PATH"
EOF

# INSTALLING PYTHON LIBRARIES
conda install -y jupyter  # interactive data analytics in the browser
conda install -y jupyterlab  # Jupyter Lab environment
conda install -y numpy  #  numerical computing package
conda install -y pytables  # wrapper for HDF5 binary storage
conda install -y pandas  #  data analysis package
conda install -y scipy  #  scientific computations package
conda install -y matplotlib  # standard plotting library
conda install -y seaborn  # statistical plotting library
conda install -y quandl  # wrapper for Quandl data API
conda install -y scikit-learn  # machine learning library
conda install -y openpyxl  # package for Excel interaction
conda install -y xlrd xlwt  # packages for Excel interaction
conda install -y pyyaml  # package to manage yaml files

pip install --upgrade pip  # upgrading the package manager
pip install q  # logging and debugging
pip install plotly  # interactive D3.js plots
pip install cufflinks  # combining plotly with pandas
pip install tensorflow  # deep learning library
pip install keras  # deep learning library
pip install eikon  # Python wrapper for the Refinitiv Eikon Data API
# Python wrapper for Oanda API
pip install git+git://github.com/yhilpisch/tpqoa

# COPYING FILES AND CREATING DIRECTORIES
mkdir -p /root/.jupyter/custom
wget http://hilpisch.com/custom.css
mv custom.css /root/.jupyter/custom
mv /root/jupyter_notebook_config.py /root/.jupyter/
mv /root/mycert.pem /root/.jupyter
mv /root/mykey.key /root/.jupyter
mkdir /root/notebook
cd /root/notebook

# STARTING JUPYTER LAB
jupyter lab &
```

此脚本需要复制到 Droplet，并需要由编排脚本启动，如下一小节所述。

## 编写 Droplet 设置的脚本

第二个设置 Droplet 的 Bash 脚本是最短的一个（见示例 2-5）。它主要是将所有其他文件复制到 Droplet 中，需要 IP 地址作为参数。在最后一行，它启动`install.sh` bash 脚本，该脚本本身进行安装并启动`Jupyter Lab`服务器。

##### 示例 2-5\. 设置 Droplet 的 Bash 脚本

```py
#!/bin/bash
#
# Setting up a DigitalOcean Droplet
# with Basic Python Stack
# and Jupyter Notebook
#
# Python for Algorithmic Trading
# (c) Dr Yves J Hilpisch
# The Python Quants GmbH
#

# IP ADDRESS FROM PARAMETER
MASTER_IP=$1

# COPYING THE FILES
scp install.sh root@${MASTER_IP}:
scp mycert.pem mykey.key jupyter_notebook_config.py root@${MASTER_IP}:

# EXECUTING THE INSTALLATION SCRIPT
ssh root@${MASTER_IP} bash /root/install.sh
```

现在一切都准备好尝试设置代码了。在 DigitalOcean 上，创建一个新的 Droplet，选择与以下选项类似的设置：

操作系统

Ubuntu 20.04 LTS x64（撰写本文时的最新版本）

大小

两个核心，2GB，60GB SSD（标准 Droplet）

数据中心地区

法兰克福（因为您的作者住在德国）

SSH 密钥

为了无密码登录添加一个（新的）SSH 密钥^(10)

Droplet 名称

预先指定的名称或类似于`pyalgo`的内容

最后，点击`Create`按钮启动 Droplet 创建过程，通常需要大约一分钟。设置过程中的主要结果是 IP 地址，例如，当您选择法兰克福作为数据中心位置时，可能是 134.122.74.144。现在设置 Droplet 与接下来的步骤一样简单：

```py
(base) pro:cloud yves$ bash setup.sh 134.122.74.144
```

然而，生成的过程可能需要几分钟。当`Jupyter Lab`服务器显示类似以下消息时，表示过程已完成：

```py
[I 12:02:50.190 LabApp] Serving notebooks from local directory: /root/notebook
[I 12:02:50.190 LabApp] Jupyter Notebook 6.1.1 is running at:
[I 12:02:50.190 LabApp] https://pyalgo:8888/
```

在任何当前浏览器中，访问以下地址即可访问运行的`Jupyter Notebook`服务器（注意使用`https`协议）：

```py
https://134.122.74.144:8888
```

添加安全例外后，`Jupyter Notebook`登录屏幕会提示输入密码（在我们的情况下为`jupyter`）。现在一切准备就绪，可以通过`Jupyter Lab`、基于`IPython`的控制台以及终端窗口或文本文件编辑器在浏览器中开始 Python 开发。其他文件管理功能，如文件上传、文件删除或文件夹创建，也是可用的。

云实例，如 DigitalOcean 的实例，和`Jupyter Lab`（由`Jupyter Notebook`服务器提供支持）是 Python 开发人员和算法交易从业者的强大组合，可以使用专业计算和存储基础设施。专业的云和数据中心提供商确保您的（虚拟）机器物理安全且高度可用。使用云实例还可以使探索和开发阶段的成本保持相当低，因为使用通常按小时计费，无需签订长期协议。

# 结论

Python 不仅是本书的选择编程语言和技术平台，几乎每个领先的金融机构也是如此。然而，Python 部署可能是棘手的，有时甚至令人厌烦和焦虑。幸运的是，今天有技术可用——几乎所有这些技术都不到十年——可以帮助解决部署问题。开源软件`conda`不仅有助于 Python 软件包和虚拟环境管理。Docker 容器甚至进一步扩展了功能，可以轻松创建完整的文件系统和运行时环境，放置在技术上的“沙箱”或*容器*中。更进一步，像 DigitalOcean 这样的云提供商在几分钟内提供专业管理和安全的数据中心中的计算和存储容量，按小时计费。这与 Python 3.8 安装和安全的`Jupyter Notebook/Lab`服务器安装相结合，为 Python 开发和部署提供了专业环境，涉及 Python 用于算法交易项目。

# 参考资料和进一步资源

对于*Python 软件包管理*，请参阅以下资源：

+   [`pip`软件包管理器页面](https://pypi.python.org/pypi/pip)

+   [`conda`软件包管理器页面](http://conda.pydata.org)

+   [官方安装软件包页面](https://packaging.python.org/installing)

对于*虚拟环境管理*，请参阅以下资源：

+   [`virtualenv`环境管理器页面](https://pypi.python.org/pypi/virtualenv)

+   [`conda`环境管理页面](http://conda.pydata.org/docs/using/envs.html)

+   [`pipenv`软件包和环境管理器](https://github.com/pypa/pipenv)

有关*Docker 容器*的信息可以在[ Docker 首页](http://docker.com)等地找到，以及以下位置：

+   Matthias, Karl, 和 Sean Kane。2018。*Docker: Up and Running.* 第 2 版。Sebastopol：O’Reilly。

Robbins (2016) 提供了对 `Bash` *脚本语言*的简洁介绍和概述：

+   Robbins, Arnold. 2016\. *Bash Pocket Reference*. 2nd ed. Sebastopol: O’Reilly.

如何*安全运行公共 Jupyter Notebook/Lab 服务器*在 [Jupyter Notebook 文档](https://oreil.ly/uBEeq)中有解释。还有 `JupyterHub` 可用，允许管理多个用户的 `Jupyter Notebook` 服务器（参见 [JupyterHub](https://oreil.ly/-XLi5)）。

要在您的新账户中享受 10 美元的起始余额并注册 DigitalOcean，请访问 [*http://bit.ly/do_sign_up*](http://bit.ly/do_sign_up)。这可以支付最小 Droplet 两个月的使用费。

^(1) 最近一个名为 `pipenv` 的项目将包管理器 `pip` 的功能与虚拟环境管理器 `virtualenv` 的功能结合在一起。请参阅 [*https://github.com/pypa/pipenv*](https://github.com/pypa/pipenv)。

^(2) 在 Windows 上，您也可以在 Docker 容器中运行完全相同的命令（参见 [*https://oreil.ly/GndRR*](https://oreil.ly/GndRR)）。直接在 Windows 上工作需要进行一些调整。例如，详细了解 Docker 使用情况，请参阅 Matthias 和 Kane (2018) 的书籍。

^(3) 安装元包 `nomkl`，比如 `conda install numpy nomkl`，可以避免自动安装和使用 `mkl` 及相关其他包。

^(4) 在官方文档中，您会找到以下解释：“Python *虚拟环境*允许在特定应用程序的隔离位置安装 Python 包，而不是全局安装。”请参阅 [创建虚拟环境页面](https://oreil.ly/5Jgjc)。

^(5) 有关 Docker 技术的全面介绍，请参阅 Matthias 和 Kane (2018)。

^(6) 有关 `Bash` 脚本的简洁介绍和快速概述，请参阅 Robbins (2016) 的书籍。另请参阅 [GNU Bash](https://oreil.ly/SGHn1)。  

^(7) 对于尚未与云提供商建立帐户的人，在 [*http://bit.ly/do_sign_up*](http://bit.ly/do_sign_up) 上，新用户可获得 10 美元的起始信用额度用于 DigitalOcean。

^(8) 从技术上讲，`Jupyter Lab` 是 `Jupyter Notebook` 的扩展。但是，这两个表达有时会交替使用。

^(9) 使用这样的自动生成证书时，您可能需要在浏览器提示时添加安全异常。在 Mac OS 上，您甚至可能需要显式将证书注册为可信任。

^(10) 如果需要帮助，请访问[如何在 DigitalOcean Droplets 中使用 SSH 密钥](https://oreil.ly/Tggw7)或者[如何在 DigitalOcean Droplets 上使用 PuTTY 进行 SSH 密钥管理（Windows 用户）](https://oreil.ly/-jTif)。
