# 第二章：Python 基础设施

> 在建造房屋时，有木材选择的问题。
> 
> 重要的是，木匠的目标是携带能够良好切割的设备，并在有时间时磨削这些设备。
> 
> 宫本武藏（《五轮书》）

# 介绍

对于新接触 Python 的人来说，Python 部署似乎一切都不简单。对于可以选择安装的丰富库和包也是如此。首先，Python 不只有 *一个* 版本。 Python 有许多不同的版本，如 CPython，Jython，IronPython 或 PyPy。然后仍然存在 Python 2.7 和 3.x 世界之间的差异。[¹] 接下来，本章重点关注 *CPython*，迄今为止最流行的 Python 编程语言版本，以及 *版本 3.6*。

即使专注于 CPython 3.6（以下简称`Python`），部署也因许多其他原因变得困难：

+   解释器（标准 CPython 安装）只带有所谓的 *标准库*（例如，涵盖典型的数学函数）

+   可选的 Python 包需要单独安装 —— 而且有数百个

+   由于依赖关系和操作系统特定要求，编译/构建这些非标准包可能会很棘手

+   要注意这些依赖关系并保持版本一致性随着时间的推移（即维护）通常是繁琐且耗时的。

+   对某些包的更新和升级可能导致需要重新编译大量其他包

+   更改或替换一个包可能会在（许多）其他地方引起麻烦

幸运的是，有一些工具和策略可用于帮助解决 Python 部署问题。本章介绍了以下几种有助于 Python 部署的技术类型：

+   **包管理器**：像[`pip`](https://pypi.python.org/pypi/pip)或[`conda`](http://conda.pydata.org/docs/intro.html)这样的包管理器有助于安装、更新和删除 Python 包；它们还有助于不同包的版本一致性

+   **虚拟环境管理器**：虚拟环境管理器如[`virtualenv`](https://pypi.python.org/pypi/virtualenv)或`conda`允许并行管理多个 Python 安装（例如，在单个计算机上同时拥有 Python 2.7 和 3.6 安装，或者在不冒风险的情况下测试最新的流行 Python 包的开发版本）

+   **容器**：[Docker](http://docker.com)容器代表包含运行某个软件所需的所有系统部件的完整文件系统，如代码、运行时或系统工具；例如，你可以在运行 Mac OS 或 Windows 10 的机器上运行一个包含 Ubuntu 16.04 操作系统、Python 3.6 安装和相应 Python 代码的 Docker 容器中运行。

+   **云实例**：部署用于算法交易的 Python 代码通常需要高可用性、安全性和性能；这些要求通常只能通过使用现在以有吸引力条件提供的专业计算和存储基础设施来满足，这些基础设施形式可从相对较小到真正大型和强大的云实例；与长期租用的专用服务器相比，云实例，即虚拟服务器的一个好处是，用户通常只需支付实际使用的小时数；另一个优点是，如果需要，这些云实例可以在一两分钟内立即使用，这有助于敏捷开发和可扩展性。

本章的结构如下所示

“Conda 作为包管理器”

本节介绍了`conda`作为 Python 的包管理器。

“作为虚拟环境管理器的 Conda”

本节重点介绍`conda`作为虚拟环境管理器的功能。

“使用 Docker 容器化”

本节简要介绍 Docker 作为容器化技术，并侧重于构建具有 Python 3.6 安装的基于 Ubuntu 的容器。

“使用云实例”

本节展示了如何在云中部署 Python 和 Jupyter Notebook —— 作为强大的基于浏览器的工具套件 —— 用于 Python 开发。

本章的目标是在专业基础设施上具有正确的 Python 安装，并可用最重要的数值和数据分析包，然后这种组合作为在后续章节中实现和部署 Python 代码的骨干，无论是交互式的金融分析代码还是脚本和模块形式的代码。

# Conda 作为包管理器

虽然`conda`可以单独安装，但高效的方式是通过 Miniconda，这是一个包括`conda`作为包和虚拟环境管理器的最小 Python 发行版。

## 安装 Miniconda 3.6

您可以在[Miniconda 页面](http://conda.pydata.org/miniconda.html)上下载不同版本的 Miniconda。在接下来的内容中，假定使用 Python 3.6 64 位版本，该版本适用于 Linux、Windows 和 Mac OS。本小节的主要示例是在基于 Ubuntu 的 Docker 容器中进行会话，通过 `wget` 下载 Linux 64 位安装程序，然后安装 Miniconda。如所示的代码应在任何其他基于 Linux 或 Mac OS 的机器上无需修改即可正常工作。

```py
$ docker run -ti -h py4fi -p 9999:9999 ubuntu:latest /bin/bash

root@py4fi:/# apt-get update; apt-get upgrade -y
...
root@py4fi:/# apt-get install wget bzip2 gcc
...
root@py4fi:/# wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
--2017-11-04 10:52:09--  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
Resolving repo.continuum.io (repo.continuum.io)... 104.16.19.10, 104.16.18.10, 2400:cb00:2048:1::6810:120a, ...
Connecting to repo.continuum.io (repo.continuum.io)|104.16.19.10|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 54167345 (52M) [application/x-sh]
Saving to: 'Miniconda3-latest-Linux-x86_64.sh'

Miniconda3-latest-Lin 100%[=======================>]  51.66M  1.57MB/s    in 32s

2017-11-04 10:52:41 (1.62 MB/s) - 'Miniconda3-latest-Linux-x86_64.sh' saved [54167345/54167345]

root@py4fi:/# bash Miniconda3-latest-Linux-x86_64.sh

Welcome to Miniconda3 4.3.30

In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
>>>
```

简单地按下`ENTER`键即可开始安装过程。在审查许可协议后，通过回答`yes`来批准条款。

```py
Do you approve the license terms? [yes|no]
>>> yes

Miniconda3 will now be installed into this location:
/root/miniconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/root/miniconda3] >>>
PREFIX=/root/miniconda3
installing: python-3.6.3-hc9025b9_1 ...
Python 3.6.3 :: Anaconda, Inc.
installing: ca-certificates-2017.08.26-h1d4fec5_0 ...
installing: conda-env-2.6.0-h36134e3_1 ...
installing: libgcc-ng-7.2.0-h7cc24e2_2 ...
installing: libstdcxx-ng-7.2.0-h7a57d05_2 ...
installing: libffi-3.2.1-h4deb6c0_3 ...
installing: ncurses-6.0-h06874d7_1 ...
installing: openssl-1.0.2l-h077ae2c_5 ...
installing: tk-8.6.7-h5979e9b_1 ...
installing: xz-5.2.3-h2bcbf08_1 ...
installing: yaml-0.1.7-h96e3832_1 ...
installing: zlib-1.2.11-hfbfcf68_1 ...
installing: libedit-3.1-heed3624_0 ...
installing: readline-7.0-hac23ff0_3 ...
installing: sqlite-3.20.1-h6d8b0f3_1 ...
installing: asn1crypto-0.22.0-py36h265ca7c_1 ...
installing: certifi-2017.7.27.1-py36h8b7b77e_0 ...
installing: chardet-3.0.4-py36h0f667ec_1 ...
installing: idna-2.6-py36h82fb2a8_1 ...
installing: pycosat-0.6.2-py36h1a0ea17_1 ...
installing: pycparser-2.18-py36hf9f622e_1 ...
installing: pysocks-1.6.7-py36hd97a5b1_1 ...
installing: ruamel_yaml-0.11.14-py36ha2fb22d_2 ...
installing: six-1.10.0-py36hcac75e4_1 ...
installing: cffi-1.10.0-py36had8d393_1 ...
installing: setuptools-36.5.0-py36he42e2e1_0 ...
installing: cryptography-2.0.3-py36ha225213_1 ...
installing: wheel-0.29.0-py36he7f4e38_1 ...
installing: pip-9.0.1-py36h8ec8b28_3 ...
installing: pyopenssl-17.2.0-py36h5cc804b_0 ...
installing: urllib3-1.22-py36hbe7ace6_0 ...
installing: requests-2.18.4-py36he2e5f8d_1 ...
installing: conda-4.3.30-py36h5d9f9f4_0 ...
installation finished.
```

在您同意许可条款并确认安装位置之后，您应该允许 Miniconda 将新的 Miniconda 安装位置添加到 `PATH` 环境变量中，通过再次回答 `yes`。

```py
Do you wish the installer to prepend the Miniconda3 install location
to PATH in your /root/.bashrc ? [yes|no]
[no] >>> yes
```

完成这个相当简单的安装过程后，现在既有一个基本的 Python 安装，也有`conda`可用。基本的 Python 安装已经包含了一些不错的功能，比如 [`SQLite3`](https://sqlite.org) 数据库引擎。您可以尝试看看是否可以在*新的 shell 实例*中启动 Python，或者在*追加相关路径*到相应的环境变量后启动 Python。

```py
root@py4fi:/# export PATH="/root/miniconda3/bin/:$PATH"
root@py4fi:/# python
Python 3.6.3 |Anaconda, Inc.| (default, Oct 13 2017, 12:02:49)
[GCC 7.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print('Hello Algo Trading World.')
Hello Algo Trading World.
>>> exit()
root@py4fi:/#
```

## Conda 基本操作

`Conda` 可以高效地处理，安装，更新和删除 Python 包，等等。以下列表提供了主要功能的概述。

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

有了这些功能，例如，安装`NumPy`——作为所谓科学堆栈中最重要的库之一——只需一条命令。当在装有英特尔处理器的机器上进行安装时，该过程会自动安装[英特尔数学核心库 `mkl`](https://docs.continuum.io/mkl-optimizations/)，这不仅加速了在英特尔机器上的`NumPy`的数值操作，还加速了其他几个科学 Python 包的数值操作。

```py
root@py4fi:/# conda install numpy
Fetching package metadata ...........
Solving package specifications: .

Package plan for installation in environment /root/miniconda3:

The following NEW packages will be INSTALLED:

    intel-openmp: 2018.0.0-h15fc484_7
    mkl:          2018.0.0-hb491cac_4
    numpy:        1.13.3-py36ha12f23b_0

Proceed ([y]/n)? y

intel-openmp-2 100% |######################################| Time: 0:00:00   1.78 MB/s
mkl-2018.0.0-h 100% |######################################| Time: 0:01:27   2.08 MB/s
numpy-1.13.3-p 100% |######################################| Time: 0:00:02   1.36 MB/s
root@py4fi:/#
```

也可以一次性安装多个包。`-y` 标志表示所有（可能的）问题都应回答“是”。

```py
conda install -y ipython matplotlib pandas pytables scipy seaborn
```

安装完成后，除了标准库之外，一些金融分析中最重要的库也可用。

[IPython](http://ipython.org)

一个改进的交互式 Python shell

[matplotlib](http://matplotlib.org)

Python 中的标准绘图库

[NumPy](http://numpy.org)

高效处理数值数组

[pandas](http://pandas.pydata.org)

管理表格数据，如金融时间序列数据

[PyTables](http://pytables.org)

用于[HDF5](http://hdfgroup.org)库的 Python 封装

[SciPy](http://scipy.org)

一组科学类和函数（作为依赖项安装）

[Seaborn](http://seaborn.pydata.org)

一个绘图库，添加了统计功能和良好的绘图默认值

这提供了一套用于一般数据分析和金融分析的基本工具集。下面的示例使用 `IPython` 并使用 `NumPy` 绘制一组伪随机数。

```py
root@py4fi:/# ipython
Python 3.6.3 |Anaconda, Inc.| (default, Oct 13 2017, 12:02:49)
Type 'copyright', 'credits' or 'license' for more information
IPython 6.1.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: import numpy as np

In [2]: np.random.standard_normal((5, 4))
Out[2]:
array([[-0.11906643,  0.65717731,  0.3763829 ,  0.04928928],
       [-0.88728758, -0.41263027, -0.70284906, -0.42948971],
       [-0.43964908, -0.21453161,  0.63860994, -0.24972815],
       [-0.50119552, -0.68807591, -0.03928424,  3.03200697],
       [-0.19549982,  0.92946315, -1.86523627,  0.37608079]])
In [3]: exit
root@py4fi:/#
```

执行 `conda list` 可以验证安装了哪些包。

```py
root@py4fi:/# conda list
# packages in environment at /root/miniconda3:
#
asn1crypto                0.22.0           py36h265ca7c_1
bzip2                     1.0.6                h0376d23_1
ca-certificates           2017.08.26           h1d4fec5_0
certifi                   2017.7.27.1      py36h8b7b77e_0
cffi                      1.10.0           py36had8d393_1
chardet                   3.0.4            py36h0f667ec_1
conda                     4.3.30           py36h5d9f9f4_0
conda-env                 2.6.0                h36134e3_1
cryptography              2.0.3            py36ha225213_1
cycler                    0.10.0           py36h93f1223_0
...
matplotlib                2.1.0            py36hba5de38_0
mkl                       2018.0.0             hb491cac_4
ncurses                   6.0                  h06874d7_1
numexpr                   2.6.2            py36hdd3393f_1
numpy                     1.13.3           py36ha12f23b_0
openssl                   1.0.2l               h077ae2c_5
pandas                    0.20.3           py36h842e28d_2
...
python                    3.6.3                hc9025b9_1
python-dateutil           2.6.1            py36h88d3b88_1
pytz                      2017.2           py36hc2ccc2a_1
qt                        5.6.2               h974d657_12
readline                  7.0                  hac23ff0_3
requests                  2.18.4           py36he2e5f8d_1
ruamel_yaml               0.11.14          py36ha2fb22d_2
scipy                     0.19.1           py36h9976243_3
seaborn                   0.8.0            py36h197244f_0
setuptools                36.5.0           py36he42e2e1_0
simplegeneric             0.8.1            py36h2cb9092_0
sip                       4.18.1           py36h51ed4ed_2
six                       1.10.0           py36hcac75e4_1
sqlite                    3.20.1               h6d8b0f3_1
statsmodels               0.8.0            py36h8533d0b_0
tk                        8.6.7                h5979e9b_1
tornado                   4.5.2            py36h1283b2a_0
traitlets                 4.3.2            py36h674d592_0
urllib3                   1.22             py36hbe7ace6_0
wcwidth                   0.1.7            py36hdf4376a_0
wheel                     0.29.0           py36he7f4e38_1
xz                        5.2.3                h2bcbf08_1
yaml                      0.1.7                h96e3832_1
zlib                      1.2.11               hfbfcf68_1
root@py4fi:/#
```

如果一个包不再需要，则可以使用 `conda remove` 高效地移除它。

```py
root@py4fi:/# conda remove seaborn
Fetching package metadata ...........
Solving package specifications: .

Package plan for package removal in environment /root/miniconda3:

The following packages will be REMOVED:

    seaborn: 0.8.0-py36h197244f_0

Proceed ([y]/n)? y

root@py4fi:/#
```

作为包管理器的`conda`已经非常有用。但是，只有在添加虚拟环境管理时，其全部功能才会显现出来。

###### 提示

`conda` 作为一个包管理器，使得安装、更新和移除 Python 包变得愉快。不再需要自行构建和编译包 — 有时候这可能会很棘手，因为一个包指定了一长串依赖项，而且还要考虑到在不同操作系统上的特定情况。

# Conda 作为虚拟环境管理器

安装了包含 `conda` 的 Miniconda 后，会根据选择的 Miniconda 版本提供一个默认的 Python 安装。`conda` 的虚拟环境管理功能允许在 Python 3.6 默认安装中添加一个完全独立的 Python 2.7.x 安装。为此，`conda` 提供了以下功能。

创建一个虚拟环境

`conda create --name $ENVIRONMENT_NAME`

激活一个环境

`source activate $ENVIRONMENT_NAME`

停用一个环境

`source deactivate $ENVIRONMENT_NAME`

移除一个环境

`conda-env remove --name $ENVIRONMENT_NAME`

导出到一个环境文件

`conda env export > $FILE_NAME`

从文件创建一个环境

`conda env create -f $FILE_NAME`

列出所有环境

`conda info --envs`

作为一个简单的示例，接下来的示例代码创建了一个名为 `py27` 的环境，安装了 `IPython` 并执行了一行 Python 2.7.x 代码。

```py
root@py4fi:/# conda create --name py27 python=2.7
Fetching package metadata ...........
Solving package specifications: .

Package plan for installation in environment /root/miniconda3/envs/py27:

The following NEW packages will be INSTALLED:

    ca-certificates: 2017.08.26-h1d4fec5_0
    certifi:         2017.7.27.1-py27h9ceb091_0
    libedit:         3.1-heed3624_0
    libffi:          3.2.1-h4deb6c0_3
    libgcc-ng:       7.2.0-h7cc24e2_2
    libstdcxx-ng:    7.2.0-h7a57d05_2
    ncurses:         6.0-h06874d7_1
    openssl:         1.0.2l-h077ae2c_5
    pip:             9.0.1-py27ha730c48_4
    python:          2.7.14-h89e7a4a_22
    readline:        7.0-hac23ff0_3
    setuptools:      36.5.0-py27h68b189e_0
    sqlite:          3.20.1-h6d8b0f3_1
    tk:              8.6.7-h5979e9b_1
    wheel:           0.29.0-py27h411dd7b_1
    zlib:            1.2.11-hfbfcf68_1

Proceed ([y]/n)? y

Fetching package metadata ...........
Solving package specifications: .

...

#
# To activate this environment, use:
# > source activate py27
#
# To deactivate an active environment, use:
# > source deactivate
#

root@py4fi:/#
```

注意，在激活环境后提示符如何变为 `(py27)`。²

```py
root@py4fi:/# source activate py27
(py27) root@py4fi:/# conda install -y ipython
Fetching package metadata ...........
Solving package specifications: .

...
```

最后，使用 Python 2.7 语法的 `IPython`。

```py
(py27) root@py4fi:/# ipython
Python 2.7.14 |Anaconda, Inc.| (default, Oct 27 2017, 18:21:12)
Type "copyright", "credits" or "license" for more information.

IPython 5.4.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: print "Hello Algo Trading World!"
Hello Algo Trading World!

In [2]: exit
(py27) root@py4fi:/#
```

正如这个例子所示，`conda` 作为一个虚拟环境管理器允许在一起安装不同的 Python 版本。它还允许安装某些包的不同版本。默认 Python 安装不受这种过程的影响，也不受同一台机器上可能存在的其他环境的影响。所有可用的环境可以通过 `conda info --envs` 显示。

```py
(py27) root@py4fi:/# conda info --envs
# conda environments:
#
py27                  *  /root/miniconda3/envs/py27
root                     /root/miniconda3

(py27) root@py4fi:/#
```

有时需要与他人共享环境信息或在多台机器上使用环境信息。为此，可以使用 `conda env export` 将安装的包列表导出到一个文件中。

```py
(py27) root@py4fi:/# conda env export > py27env.yml
(py27) root@py4fi:/# cat py27env.yml
name: py27
channels:
- defaults
dependencies:
- backports=1.0=py27h63c9359_1
- backports.shutil_get_terminal_size=1.0.0=py27h5bc021e_2
- ca-certificates=2017.08.26=h1d4fec5_0
- certifi=2017.7.27.1=py27h9ceb091_0
- decorator=4.1.2=py27h1544723_0
- enum34=1.1.6=py27h99a27e9_1
- ipython=5.4.1=py27h36c99b6_1
- ipython_genutils=0.2.0=py27h89fb69b_0
- libedit=3.1=heed3624_0
- libffi=3.2.1=h4deb6c0_3
- libgcc-ng=7.2.0=h7cc24e2_2
- libstdcxx-ng=7.2.0=h7a57d05_2
- ncurses=6.0=h06874d7_1
- openssl=1.0.2l=h077ae2c_5
- pathlib2=2.3.0=py27h6e9d198_0
- pexpect=4.2.1=py27hcf82287_0
- pickleshare=0.7.4=py27h09770e1_0
- pip=9.0.1=py27ha730c48_4
- prompt_toolkit=1.0.15=py27h1b593e1_0
- ptyprocess=0.5.2=py27h4ccb14c_0
- pygments=2.2.0=py27h4a8b6f5_0
- python=2.7.14=h89e7a4a_22
- readline=7.0=hac23ff0_3
- scandir=1.6=py27hf7388dc_0
- setuptools=36.5.0=py27h68b189e_0
- simplegeneric=0.8.1=py27h19e43cd_0
- six=1.11.0=py27h5f960f1_1
- sqlite=3.20.1=h6d8b0f3_1
- tk=8.6.7=h5979e9b_1
- traitlets=4.3.2=py27hd6ce930_0
- wcwidth=0.1.7=py27h9e3e1ab_0
- wheel=0.29.0=py27h411dd7b_1
- zlib=1.2.11=hfbfcf68_1
- pip:
  - backports.shutil-get-terminal-size==1.0.0
  - ipython-genutils==0.2.0
  - prompt-toolkit==1.0.15
prefix: /root/miniconda3/envs/py27

(py27) root@py4fi:/#
```

通常，虚拟环境（从技术上讲，不过是一种特定的（子）文件夹结构）是为了进行一些快速测试而创建的。在这种情况下，通过 `conda env remove` 轻松删除环境。

```py
(py27) root@py4fi:/# source deactivate
root@py4fi:/# conda env remove --name py27

Package plan for package removal in environment /root/miniconda3/envs/py27:

The following packages will be REMOVED:

    backports:                          1.0-py27h63c9359_1
    backports.shutil_get_terminal_size: 1.0.0-py27h5bc021e_2
    ca-certificates:                    2017.08.26-h1d4fec5_0
    certifi:                            2017.7.27.1-py27h9ceb091_0
    ...
    traitlets:                          4.3.2-py27hd6ce930_0
    wcwidth:                            0.1.7-py27h9e3e1ab_0
    wheel:                              0.29.0-py27h411dd7b_1
    zlib:                               1.2.11-hfbfcf68_1

Proceed ([y]/n)? y
                                                                           #
```

这就结束了 `conda` 作为虚拟环境管理器的概述。

###### 提示

`conda` 不仅帮助管理包，还是 Python 的虚拟环境管理器。它简化了不同 Python 环境的创建，允许在同一台机器上有多个 Python 版本和可选包可用，而且它们之间互不影响。`conda` 还允许将环境信息导出，以便在多台机器上轻松复制它或与他人共享。

# 使用 Docker 容器化

Docker 容器已经风靡了 IT 世界。尽管技术仍然很年轻，但它已经确立了自己作为几乎任何类型软件应用的高效开发和部署的基准之一。

对于我们的目的，将 Docker 容器视为一个分离的（“容器化的”）文件系统足以包含操作系统（例如服务器上的 Ubuntu 16.04），一个（Python）运行时，额外的系统和开发工具以及根据需要的其他（Python）库和软件包。这样的 Docker 容器可以在具有 Windows 10 的本地机器上运行，也可以在具有 Linux 操作系统的云实例上运行，例如。

本节不允许深入探讨 Docker 容器的有趣细节。它更多地是对 Docker 技术在 Python 部署上的简要说明。⁴

## Docker 镜像和容器

然而，在进入说明之前，谈论 Docker 时需要区分两个基本术语。第一个是*Docker 镜像*，可以类比为 Python 类。第二个是*Docker 容器*，可以类比为相应 Python 类的实例。

在更技术层面上，在[Docker 术语表](https://docs.docker.com/engine/reference/glossary/)中找到了对*Docker 镜像*的以下定义：

> Docker 镜像是容器的基础。一个镜像是一组有序的根文件系统更改和相应的执行参数，用于容器运行时。一个镜像通常包含叠加在彼此上面的分层文件系统的联合。镜像没有状态，永远不会改变。

同样，您可以在[Docker 术语表](https://docs.docker.com/engine/reference/glossary/)中找到对*Docker 容器*的以下定义，它将类比为 Python 类和这些类的实例：

> 容器是 Docker 镜像的运行时实例。Docker 容器包括：一个 Docker 镜像，一个执行环境和一组标准指令。

根据操作系统的不同，安装 Docker 的方式略有不同。这就是为什么本节不涉及相应细节的原因。详细信息请参阅[安装 Docker 引擎页面](https://docs.docker.com/engine/installation/)。

## 构建一个 Ubuntu 和 Python Docker 镜像

本小节说明了基于最新版本的 Ubuntu 构建 Docker 镜像的过程，该镜像包含 Miniconda 以及一些重要的 Python 包。此外，它还通过更新 Linux 软件包索引，必要时升级软件包，并安装某些额外的系统工具来进行一些 Linux 的维护工作。为此，需要两个脚本。一个是在 Linux 级别执行所有工作的`bash`脚本。⁵另一个是所谓的`Dockerfile`，它控制镜像本身的构建过程。

示例 2-1 中的 `bash` 脚本负责安装，由三个主要部分组成。第一部分处理 Linux 的基本事务。第二部分安装 Miniconda，而第三部分安装可选的 Python 包。内联还有更详细的注释。

##### 示例 2-1\. 安装 Python 和可选包的脚本

```py
#!/bin/bash
#
# Script to Install
# Linux System Tools and
# Basic Python Components
#
# Python for Finance
# (c) Dr. Yves J. Hilpisch
#
# GENERAL LINUX
apt-get update  # updates the package index cache
apt-get upgrade -y  # updates packages
# installs system tools
apt-get install -y bzip2 gcc git htop screen vim wget
apt-get upgrade -y bash  # upgrades bash if necessary
apt-get clean  # cleans up the package index cache

# INSTALL MINICONDA
# downloads Miniconda
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O Miniconda.sh
bash Miniconda.sh -b  # installs it
rm -rf Miniconda.sh  # removes the installer
export PATH="/root/miniconda3/bin:$PATH"  # prepends the new path

# INSTALL PYTHON LIBRARIES
conda install -y pandas  # installs pandas
conda install -y ipython  # installs IPython shell
```

示例 2-2 中的 `Dockerfile` 使用 示例 2-1 中的 `bash` 脚本构建新的 Docker 镜像。它还将其主要部分作为注释内联。

##### 示例 2-2\. 构建镜像的 Dockerfile

```py
#
# Building a Docker Image with
# the Latest Ubuntu Version and
# Basic Python Install
#
# Python for Finance
# (c) Dr. Yves J. Hilpisch
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

如果这两个文件在同一个文件夹中，并且已安装 Docker，则构建新的 Docker 镜像就很简单。在这里，标签 `ubuntupython` 用于该镜像。这个标签在需要引用镜像时很重要，例如在基于它运行容器时。

```py
macbookpro:~/Docker$ docker build -t py4fi:basic .
Sending build context to Docker daemon   29.7kB
Step 1/7 : FROM ubuntu:latest
 ---> 747cb2d60bbe

...

Step 7/7 : CMD ["ipython"]
 ---> Running in fddb07301003
Removing intermediate container fddb07301003
 ---> 60a180c9cfa6
Successfully built 60a180c9cfa6
Successfully tagged py4fi:basic
macbookpro:~/Docker$
```

可以通过 `docker images` 列出现有的 Docker 镜像。新镜像应该位于列表的顶部。

```py
macbookpro:~/Docker$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
py4fi               basic               60a180c9cfa6        About a minute ago   1.65GB
ubuntu              python              9fa4649d110f        4 days ago           2.46GB
<none>              <none>              5ac1e7b7bd9f        4 days ago           2.46GB
ubuntu              base                bcee12a18154        6 days ago           2.29GB
<none>              <none>              032acb2af94c        6 days ago           122MB
tpq                 python              a9dc75f040f6        12 days ago          2.55GB
ubuntu              latest              747cb2d60bbe        3 weeks ago          122MB
macbookpro:~/Docker$
```

成功构建 `ubuntupython` 镜像后，可以使用 `docker run` 运行相应的 Docker 容器。参数组合 `-ti` 用于在 Docker 容器中运行交互式进程，比如 shell 进程（参见 [Docker Run 参考页](https://docs.docker.com/engine/reference/run/)）。

```py
macbookpro:~/Docker$ docker run -ti py4fi:basic

Python 3.6.3 |Anaconda, Inc.| (default, Oct 13 2017, 12:02:49)
Type 'copyright', 'credits' or 'license' for more information
IPython 6.1.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: import numpy as np

In [2]: a = np.random.standard_normal((5, 3))

In [3]: import pandas as pd

In [4]: df = pd.DataFrame(a, columns=['a', 'b', 'c'])

In [5]: df
Out[5]:
          a         b         c
0  0.062129  0.040233  0.494589
1 -0.681517 -0.307187 -0.476016
2  0.861527  0.438467 -1.656811
3 -1.402893  0.611978  0.238889
4  0.876606  0.746728  0.840246

In [6]:
```

退出 `IPython` 也将退出容器，因为它是容器中唯一运行的应用程序。然而，你可以通过

```py
Ctrl+p --> Ctrl+q
```

从容器中分离后，`docker ps` 命令显示运行的容器（可能还有其他当前正在运行的容器）：

```py
macbookpro:~/Dropbox/Platform/algobox/algobook/book/code/Docker$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
98b95440f962        py4fi:basic         "ipython"           3 minutes ago       Up 3 minutes                                 wonderful_neumann
4b85dfc94780        ubuntu:latest       "/bin/bash"         About an hour ago   Up About an hour    0.0.0.0:9999->9999/tcp   musing_einstein
macbookpro:~/Docker$
```

通过 `docker attach $CONTAINER_ID`（注意，容器 ID 的几个字母就足够了）连接到 Docker 容器：

```py
macbookpro:~/Docker$ docker attach 98b95
In [6]: df.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5 entries, 0 to 4
Data columns (total 3 columns):
a    5 non-null float64
b    5 non-null float64
c    5 non-null float64
dtypes: float64(3)
memory usage: 200.0 bytes

In [7]: exit
macbookpro:~/Docker$
```

`exit` 命令终止 `IPython`，从而终止 Docker 容器。它可以通过 `docker rm` 删除。

```py
macbookpro:~/Docker$ docker rm 98b95
d9efb
macbookpro:~/Docker$
```

类似地，如果不再需要，可以通过 `docker rmi` 删除 Docker 镜像 `ubuntupython`。虽然容器相对轻量级，但单个镜像可能会消耗大量存储空间。在 `py4fi:basic` 镜像的情况下，其大小超过 1 GB。这就是为什么你可能想定期清理 Docker 镜像列表的原因。

```py
macbookpro:~/Docker$ docker rmi 60a180
```

当然，关于 Docker 容器及其在某些应用场景中的优势还有很多值得说的。对于本书和在线培训课程而言，它们提供了一种现代化的方法，用于部署 Python，在完全分离的（容器化）环境中进行 Python 开发，并为算法交易提供代码。

###### 提示

如果你还没有使用 Docker 容器，应考虑开始使用它们。在 Python 部署和开发工作中，它们提供了许多好处，不仅在本地工作时，而且在使用远程云实例和服务器部署算法交易代码时尤其如此。

# 使用云实例

本节展示了如何在 [DigitalOcean](http://digitalocean.com) 云实例上设置一个完整的 Python 基础设施。还有许多其他的云服务提供商，其中包括 [亚马逊网络服务](http://aws.amazon.com)（AWS）作为领先的提供商。然而，DigitalOcean 以其简单性和相对较低的价格而闻名，他们称之为 *droplet* 的较小的云实例。最小的 droplet，通常足够用于探索和开发目的，每月只需 5 美元或每小时 0.007 美元。用户只按小时计费，因此您可以轻松地为 2 小时启动一个 droplet，然后销毁它，仅需收取 0.014 美元。如果您还没有账户，请在这个 [注册页面](https://m.do.co/c/fbe512dd3dac) 上注册一个账户，这可以为您提供 10 美元的起始信用额。

本节的目标是在 DigitalOcean 上设置一个 droplet，其中包含 Python 3.6 安装和通常所需的包（例如 `NumPy`、`pandas`），以及一个密码保护和安全套接字层（SSL）加密的 [Jupyter Notebook](http://jupyter.org) 服务器安装。作为一个基于 Web 的工具套件，Jupyter Notebook 提供了三个主要工具，可以通过常规浏览器使用：

+   **Jupyter Notebook**：这是目前非常流行的交互式开发环境，具有不同语言内核的选择，如 Python、R 和 Julia。

+   **终端**：通过浏览器访问的系统 shell 实现，允许进行所有典型的系统管理任务，但也可以使用诸如 [`Vim`](http://www.vim.org/download.ph) 或 [`git`](https://git-scm.com/) 等有用工具。

+   **编辑器**：第三个主要工具是一个基于浏览器的文件编辑器，具有许多不同的编程语言和文件类型的语法突出显示以及典型的编辑功能。

在一个 droplet 上安装 Jupyter Notebook 允许通过浏览器进行 Python 开发和部署，避免了通过安全外壳（SSH）访问登录云实例的需求。

为了完成本节的目标，需要一些文件。

+   **服务器设置脚本**：这个脚本协调所有必要的步骤，比如将其他文件复制到 droplet 上并在 droplet 上运行它们。

+   **Python 和 Jupyter 安装脚本**：这个脚本安装 Python、额外的包、Jupyter Notebook 并启动 Jupyter Notebook 服务器。

+   **Jupyter Notebook 配置文件**：此文件用于配置 Jupyter Notebook 服务器，例如密码保护方面。

+   **RSA 公钥和私钥文件**：这两个文件用于对 Jupyter Notebook 服务器进行 SSL 加密。

接下来，我们将逆向处理这个文件列表。

## RSA 公钥和私钥

为了通过任意浏览器安全连接到 Jupyter Notebook 服务器，需要一个由 RSA 公钥和私钥组成的 SSL 证书（参见[RSA 维基百科页面](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95)）。一般来说，人们期望这样的证书来自所谓的证书颁发机构（CA）。然而，在本书的目的下，自动生成的证书已经“`足够好`“了。[⁶] 生成 RSA 密钥对的流行工具是[`OpenSSL`](http://openssl.org)。接下来的简要交互式会话生成了适用于 Jupyter Notebook 服务器的证书。

```py
macbookpro:~/cloud$ openssl req -x509 -nodes -days 365 -newkey \
> rsa:1024 -out cert.pem -keyout cert.key
Generating a 1024 bit RSA private key
..++++++
.......++++++
writing new private key to 'cert.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:DE
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:Voelklingen
Organization Name (eg, company) [Internet Widgits Pty Ltd]:TPQ GmbH
Organizational Unit Name (eg, section) []:Algo Trading
Common Name (e.g. server FQDN or YOUR name) []:Jupyter
Email Address []:team@tpq.io
macbookpro:~/cloud$ ls
cert.key    cert.pem
macbookpro:~/cloud$
```

两个文件 `cert.key` 和 `cert.pem` 需要复制到滴管上，并且需要被 Jupyter Notebook 配置文件引用。下面会介绍这个文件。

## Jupyter Notebook 配置文件

可以按照[运行 Notebook 服务器页面](http://jupyter-notebook.readthedocs.io/en/latest/public_server.html)上的说明安全地部署一个公共 Jupyter Notebook 服务器。其中，Jupyter Notebook 应该受到密码保护。为此，有一个叫做 `passwd` 的密码哈希生成函数，可在 `notebook.auth` 子包中使用。下面的代码生成了一个以 `jupyter` 为密码的密码哈希代码。

```py
macbookpro:~/cloud$ ipython
Python 3.6.1 |Continuum Analytics, Inc.| (default, May 11 2017, 13:04:09)
Type 'copyright', 'credits' or 'license' for more information
IPython 6.1.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from notebook.auth import passwd

In [2]: passwd('jupyter')
Out[2]: 'sha1:4c72b4542011:0a8735a18ef2cba12fde3744886e61f76706299b'

In [3]: exit
```

此哈希代码需要放置在 Jupyter Notebook 配置文件中，如示例 2-3 所示。该代码假定 RSA 密钥文件已复制到了滴管的 `/root/.jupyter/` 文件夹中。

##### 示例 2-3. Jupyter Notebook 配置文件

```py
#
# Jupyter Notebook Configuration File
#
# Python for Finance
# (c) Dr. Yves J. Hilpisch
#
# SSL ENCRYPTION
# replace the following file names (and files used) by your choice/files
c.NotebookApp.certfile = u'/root/.jupyter/cert.pem'
c.NotebookApp.keyfile = u'/root/.jupyter/cert.key'

# IP ADDRESS AND PORT
# set ip to '*' to bind on all IP addresses of the cloud instance
c.NotebookApp.ip = '*'
# it is a good idea to set a known, fixed default port for server access
c.NotebookApp.port = 8888

# PASSWORD PROTECTION
# here: 'jupyter' as password
# replace the hash code with the one for your password
c.NotebookApp.password = 'sha1:bb5e01be158a:99465f872e0613a3041ec25b7860175f59847702'

# NO BROWSER OPTION
# prevent Jupyter from trying to open a browser
c.NotebookApp.open_browser = False
```

###### 注意

将 Jupyter Notebook 部署在云中主要会引发一系列安全问题，因为它是一个通过 Web 浏览器可访问的完整的开发环境。因此，使用 Jupyter Notebook 服务器默认提供的安全措施（如密码保护和 SSL 加密）至关重要。但这只是开始，根据在云实例上具体做了什么，可能建议采取进一步的安全措施。

下一步是确保 Python 和 Jupyter Notebook 在滴管上安装。

## Python 和 Jupyter Notebook 的安装脚本

用于安装 Python 和 Jupyter Notebook 的 bash 脚本类似于在“使用 Docker 容器化”一节中介绍的用于在 Docker 容器中通过 Miniconda 安装 Python 的脚本。然而，这里的脚本还需要启动 Jupyter Notebook 服务器。所有主要部分和代码行都在内联注释中。

##### 示例 2-4. 安装 Python 并运行 Jupyter Notebook 服务器的 bash 脚本

```py
#!/bin/bash
#
# Script to Install
# Linux System Tools and Basic Python Components
# as well as to
# Start Jupyter Notebook Server
#
# Python for Finance
# (c) Dr. Yves J. Hilpisch
#
# GENERAL LINUX
apt-get update  # updates the package index cache
apt-get upgrade -y  # updates packages
# install system tools
apt-get install -y bzip2 gcc git htop screen htop vim wget
apt-get upgrade -y bash  # upgrades bash if necessary
apt-get clean  # cleans up the package index cache

# INSTALLING MINICONDA
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O Miniconda.sh
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
conda install -y pytables  # wrapper for HDF5 binary storage
conda install -y pandas  #  data analysis package
conda install -y pandas-datareader  # retrieval of open data
conda install -y matplotlib  # standard plotting library
conda install -y seaborn  # statistical plotting library
conda install -y quandl  # wrapper for Quandl data API
conda install -y scikit-learn  # machine learning library
conda install -y tensorflow  # deep learning library
conda install -y flask  # light weight web framework
conda install -y openpyxl  # library for Excel interaction
conda install -y pyyaml  # library to manage yaml files

pip install --upgrade pip  # upgrading the package manager
pip install q  # logging and debugging
pip install plotly  # interactive D3.js plots
pip install cufflinks  # combining plotly with pandas

# COPYING FILES AND CREATING DIRECTORIES
mkdir /root/.jupyter
mv /root/jupyter_notebook_config.py /root/.jupyter/
mv /root/cert.* /root/.jupyter
mkdir /root/notebook
cd /root/notebook

# STARTING JUPYTER NOTEBOOK
jupyter notebook --allow-root
```

此脚本需要复制到滴管上，并且需要由下一小节中描述的编排脚本启动。

## 管理滴管设置的脚本

设置滴水筹的第二个 bash 脚本是最短的。它主要将所有其他文件复制到滴水筹中，其中预期的是相应的 IP 地址作为参数。在最后一行，它启动`install.sh` bash 脚本，后者又会进行安装并启动 Jupyter Notebook 服务器。

##### 示例 2-5\. 用于设置滴水筹的`bash`脚本

```py
#!/bin/bash
#
# Setting up a DigitalOcean Droplet
# with Basic Python Stack
# and Jupyter Notebook
#
# Python for Finance
# (c) Dr Yves J Hilpisch
#

# IP ADDRESS FROM PARAMETER
MASTER_IP=$1

# COPYING THE FILES
scp install.sh root@${MASTER_IP}:
scp cert.* jupyter_notebook_config.py root@${MASTER_IP}:

# EXECUTING THE INSTALLATION SCRIPT
ssh root@${MASTER_IP} bash /root/install.sh
```

现在一切都准备好尝试设置代码。在 DigitalOcean 上，创建一个新的滴水筹，并选择类似于以下选项：

+   **操作系统**：Ubuntu 16.04.3 x64（截至 2017 年 11 月 4 日的默认选择）

+   **规模**：1 核心，512 MB，20GB SSD（最小的滴水筹）

+   **数据中心区域**：法兰克福（因为作者居住在德国）

+   **SSH 密钥**：添加（新的）SSH 密钥以实现无密码登录 ^（7）

+   **滴水筹名称**：您可以选择预先指定的名称，也可以选择像`py4fi`这样的名称

最后，点击`创建`按钮启动滴水筹建过程，通常需要约一分钟。进行设置过程的主要结果是 IP 地址，例如，当您选择法兰克福作为数据中心位置时，可能为 46.101.156.199。现在设置滴水筹非常简单：

```py
(py3) macbookpro:~/cloud$ bash setup.sh 46.101.156.199
```

结果过程可能需要几分钟。当 Jupyter Notebook 服务器出现类似于以下消息时，过程结束：

```py
The Jupyter Notebook is running at: https://[all ip addresses on your system]:8888/
```

在任何当前浏览器中，访问以下地址即可访问正在运行的 Jupyter Notebook（注意`https`协议）：

```py
https://46.101.156.199:8888
```

可能需要添加安全例外，然后 Jupyter Notebook 登录屏幕会提示输入密码（在我们的情况下为`jupyter`）。现在一切准备就绪，可以通过 Jupyter Notebook 在浏览器中开始 Python 开发，通过终端窗口进行`IPython`或文本文件编辑器。还提供了其他文件管理功能，如文件上传，删除文件或创建文件夹。

###### 小贴士

像 DigitalOcean 和 Jupyter Notebook 这样的云实例是算法交易员的强大组合，可以利用专业的计算和存储基础设施。专业的云和数据中心提供商确保您的（虚拟）机器在物理上安全且高度可用。使用云实例也可以使探索和开发阶段的成本相对较低，因为通常按小时收费，而无需签订长期协议。

# 结论

Python 是本书选择的编程语言和技术平台。然而，Python 部署可能最多时甚至令人厌烦和心神不宁。幸运的是，今天有一些技术可用 — 都不到五年的时间 — 有助于解决部署问题。开源软件`conda`有助于 Python 包和虚拟环境的管理。Docker 容器甚至可以进一步扩展，可以在一个技术上受到保护的"`沙箱`"中轻松创建完整的文件系统和运行时环境。更进一步，像 DigitalOcean 这样的云提供商在几分钟内提供由专业管理和安全的数据中心提供的计算和存储容量，并按小时计费。这与 Python 3.6 安装和安全的 Jupyter Notebook 服务器安装结合在一起，为算法交易项目的 Python 开发和部署提供了专业的环境。

# 进一步资源

对于*Python 包管理*，请参考以下资源：

+   [`pip`包管理器页面](https://pypi.python.org/pypi/pip)

+   [`conda`包管理器页面](http://conda.pydata.org)

+   [官方安装包页面](https://packaging.python.org/installing/)

对于*虚拟环境管理*，请参阅以下资源：

+   [`virtualenv`环境管理器页面](https://pypi.python.org/pypi/virtualenv)

+   [`conda`环境管理页面](http://conda.pydata.org/docs/using/envs.html)

关于*Docker 容器*的信息请见此处：

+   [Docker 首页](http://docker.com)

+   Matthias、Karl 和 Sean Kane (2015): *Docker: Up and Running.* O’Reilly, 北京等。

Robbins (2016)提供了对`bash` *脚本语言*的简明介绍和概述。

+   Robbins, Arnold (2016): *Bash Pocket Reference*. 第 2 版，O’Reilly, 北京等。

如何安全运行*公共 Jupyter Notebook 服务器*请参阅[运行笔记本服务器](http://jupyter-notebook.readthedocs.io/en/latest/public_server.html)。

要在 DigitalOcean 上注册一个新帐户并获得 10 美元的起始信用，请访问此[注册页面](https://m.do.co/c/fbe512dd3dac)。这可以支付最小水滴两个月的使用费。

¹在撰写本文时，Python 3.7beta 刚刚发布。

²在 Windows 上，激活新环境的命令仅为`activate py27` — 省略了`source`。

³在官方文档中，您会找到以下解释：“`Python '虚拟环境'允许将 Python 包安装在特定应用程序的隔离位置，而不是全局安装。`"参见[创建虚拟环境页面](https://packaging.python.org/installing/#creating-virtual-environments)。

⁴ 有关 Docker 技术的全面介绍，请参阅 Matthias 和 Kane (2015) 的书籍。

⁵ 要了解 `bash` 脚本的简明介绍和快速概述，请参考 Robbins (2016) 的书籍。

⁶ 使用这样一个自行生成的证书时，可能需要在浏览器提示时添加安全异常。

⁷ 如果需要帮助，请访问[如何在 DigitalOcean Droplets 上使用 SSH 密钥](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)或[如何在 DigitalOcean Droplets 上使用 PuTTY（Windows 用户）](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-putty-on-digitalocean-droplets-windows-users)。
