# 第二章 第 1 节 pandas、numpy 矢量化编程（上）

> 原文：[`www.nowcoder.com/tutorial/10067/6b6d077033f641ef94f6bb784bab1073`](https://www.nowcoder.com/tutorial/10067/6b6d077033f641ef94f6bb784bab1073)

# 一、写在前面

众所周知，pandas 是一款用于处理和分析数据的 python 第三方库，它提供了强大的数据模型(如 DataFrame 和 Series)、便捷的数据处理函数和方法(如缺失值的处理、重复值的处理)，并可以与机器学习框架(比如 scikit-learn)无缝地融合，因而成为数据分析、机器学习、数据科学领域必备的工具，并广泛运用在包括金融、经济、统计、分析等学术和商业领域。

然而，在享受 pandas 的强大和便利的同时，有时候，会觉得 pandas 运行十分缓慢。
这个时候就要检查一下是否可能使用了逐个元素遍历的方式来遍历 DataFrame。这种遍历的方式可能是使用 python 的“后遗症”，因为在 python 中，我们经常会使用这种方式来遍历列表或元组。但这种方式放在 pandas 中就不是很高效，因为循环遍历每个元素、行或列并不是 pandas 设计的目的，因为 Pandas 是为一次性处理整个行或列的矢量化操作而设计的；除此之外，也没有发挥出 pandas 本身内置的性能优化这一特性。

写到这里，想先对矢量(或向量)编程下一个概念：与**逐个**遍历元素并进行处理不同的是，把 DataFrame**整行或整列**进行一次性处理的方式(或者简单理解为没有 for 循环的方法和函数)称为矢量化。这里的行或列，在 pandas 中体现为 DataFrame(行或列)或 Series，而在 numpy 中为 array。pandas 是为一次性处理整个行或列的矢量化操作而设计的。

如果你不是很理解上面这一段话，我给举一个比较生活化的例子：假设大学军训的时候，教官想让第一排同学向右转，这时教官有两种做法：第一种就是挨个跟第一排的同学说“向右转”，这时候有几个同学，教官就需要说几次；第二种就是让第一排的同学全部右转。很明显，第一种方式就是我们非常熟悉的 for 循环方法，而第二种可以理解为就是 pandas 与 numpy 的矢量化操作。

矢量化的目的在于提升速度，所以这篇文章会首先对比非矢量化编程与矢量化编程，直观地感受两者在速度上的差别；然后会对比两种矢量化编程的方式——pandas 矢量化和 numpy 矢量化，接着介绍这两种方式的具体使用；最后补充 apply 相关函数的介绍，它在矢量化无法使用或者使用循环的方式时会比较高效。

* * *

## 二、非矢量化 VS 矢量化

在对矢量化编程给出进一步解释之前，先来直观地感受一下矢量化与非矢量化的差别到底在哪里。

实验环境介绍：

```cpp
import pandas as pd 
import numpy as np
import platform

# 操作系统为 mac os 10.15.5

# python 版本
platform.python_version() # 3.7.7

# pandas 版本
pd.__version__ # '1.0.4'

# numpy 版本
np.__version__ # '1.18.5'
```

数据集准备

数据集使用 scikit learn 自带的——Boston 房价数据集。我们想用它的一些特征来预测房价。

```cpp
import pandas as pd 
import numpy as np
from sklearn.datasets import load_boston

# 加载数据集
boston = load_boston()

# 查看数据集的特征有哪些
boston.feature_names

"""
array(['CRIM', 'ZN', 'INDUS', 'CHAS', 'NOX', 'RM', 'AGE', 'DIS', 'RAD',
       'TAX', 'PTRATIO', 'B', 'LSTAT'], dtype='<U7')
"""
# 各个特征的相关介绍
boston.DESCR

# 构造 DataFrame
data = pd.DataFrame(boston.data,columns=boston.feature_names)
```

出于演示的目的，假定房价只与三个因素 AGE(房子的年龄，从 1940 开始算起)、DIS(到 Boston 五个就业中心的距离)以及 TAX(每 10,000 美元的财产税率)有关，且关系为![](img/c1fe6526f2bffb4044b7dc44ec5dcb6b.png)。

房价计算的相关代码如下：

```cpp
def price(age,distances,tax):
    return age **2  + distances ** 3 + tax ** 4
```

#### 1、非矢量化编程

使用**逐行逐列**遍历的方式计算：

```cpp
def price_looping(df):
    price_list = []
    for i in range(0, len(data)):
        d = price(df.iloc[i]['AGE'], df.iloc[i]['DIS'],df.iloc[i]['TAX'])
        price_list.append(d)
    return price_list
%%timeit 
data['price'] = price_looping(data)

"""
229 ms ± 6.97 ms per loop (mean ± std. dev. of 7 runs, 1 loop each)
"""
```

timeit 是用来测量一小片代码的执行时间，它是 python 的标准库，链接为：[`docs.python.org/3/library/timeit.html`](https://docs.python.org/3/library/timeit.html)

因为我使用的是 jupyter notebook，%%timeit 表示将对 notebook 里面的这个 cell 进行计时。它的工作原理是先把当前 cell 里的函数运行多次，然后计算耗时的平均值和标准差。可以将它作为一个基准工具，比较不同函数在同一操作系统和同一数据集上的速度。

通过执行上面的代码，我们计算出了使用逐行逐列方式进行处理的时间为 229ms，感觉上是很快的样子，但实际上非常慢，因为 DataFrame 是一个拥有行和列的对象，逐行逐列意味着会遍历每一个元素，并且我们只处理了 506 行(boston 数据集只有 506 行)数据。

接下来看一下使用 pandas 内置函数 iterrows()**，**它其实是一个生成器，会对 DataFrame 以索引的方式逐行迭代，并为每一行返回一个 Series 及其索引。下面是它的耗时：

```cpp
%%timeit
price_series = []
for index, row in data.iterrows():
    price_series.append(price(row['AGE'], row['DIS'],row['TAX']))

data['price'] = price_series

"""
53.3 ms ± 2.12 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
"""
```

先来看一下代码执行时间——53.3ms，而逐行逐列方式为 229ms，所以这种方式约占上一种方式的四分之一。上一种方式使用了 range()函数，而 iterrows 函数内部是生成器，在循环时，生成器比传统的 for 循环快得多，具体可以查看一下生成器的工作原理。尽管 iterrows()函数看起来很优秀，但它并不是最快的，甚至可以说是这几种方式中第二慢的。

下面是 iterrows()迭代一行产生的结果：

```cpp
row = next(data.iterrows())[1]
"""

CRIM        6.320000e-03
ZN          1.800000e+01
INDUS       2.310000e+00
CHAS        0.000000e+00
NOX         5.380000e-01
RM          6.575000e+00
AGE         6.520000e+01
DIS         4.090000e+00
RAD         1.000000e+00
TAX         2.960000e+02
PTRATIO     1.530000e+01
B           3.969000e+02
LSTAT       4.980000e+00
price       7.676568e+09
distance    7.676568e+09
Name: 0, dtype: float64

"""
```

接下来看一下使用 apply 函数的效果。代码如下：

```cpp
%%timeit

# 使用 apply 和 lambda 函数
data['price'] = data.apply(lambda row: price(row['AGE'], row['DIS'],row['TAX']), axis=1)

"""
23.4 ms ± 659 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
"""
```

从上面的结果可以看出，apply 函数在 iterrows 函数的基础上又提高了一倍多，从 53.3 ms 降低到了 23.4 ms。

apply 的工作原理是接受一个函数并沿 DataFrame 的轴(所有行或所有列)来应用它们，在稍后的部分也会介绍 apply 及相关函数(比如 applymap、map)的使用。apply 看起来也是像 iterrows 函数一样在一行行迭代遍历，但是 apply 要更高效一些，因为它涉及到了一些优化，比如在 Cython 空间中执行迭代(取决于函数的内容)。要知道，pandas 的很多操作都是通过 numpy 或者 pandas 自身由 Cython 实现并编译成 C 的扩展模块在 C 语言中实现的。但 iterrows 函数迭代每一行并将其构建到一个 Series 中，这个操是在 python 空间中执行的，因此与 apply 相比耗时是非常巨大的。

我们先来验证一下 apply 函数的迭代次数。

我会使用 line_profiler 这个工具，它可以逐行地对函数执行过程进行分析，并收集相关信息，链接为：[`github.com/rkern/line_profiler#installation`](https://github.com/rkern/line_profiler#installation)

首先使用 pip 命令进行安装：

```cpp
pip install line_profiler
# juypter notebook 中加载 line_profiler

%load_ext line_profiler
# 使用 line_profiler 分析 apply 函数
%lprun -f price data.apply(lambda row: price(row['AGE'], row['DIS'],row['TAX']), axis=1)
```

结果如下：

```cpp
"""
Timer unit: 1e-06 s

Total time: 0.001778 s
File: <ipython-input-98-adc0838a8a0c>
Function: price at line 1

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     1                                           def price(age,distances,tax):
     2       506       1778.0      3.5    100.0      return age **2  + distances ** 3 + tax ** 4

"""
```

从结果可以看出，apply 函数迭代了 506 次(Hits 表示这行代码执行的次数)，因为我们有 506 行数据。price 函数只有一行代码，因此这行代码占用了全部的时间。

#### 2、矢量化编程

从上面的实验可以看出，apply 函数也进行了迭代，但事实上如果可以避免迭代，直接将函数作用于整个列，将会获得巨大的速度增益，这就是 pandas 的矢量化编程，它是在 Pandas 中执行的最快方法。pandas Series 矢量化代码如下：

```cpp
%%timeit 

# Pandas Series 矢量化:将整个列传入函数，而非单个元素
data['price'] = price(data['AGE'], data['DIS'],data['TAX'])

"""
942 µs ± 19 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
"""
```

从上面代码及其执行结果可以看到，pandas 矢量化的方式会同时在多个列上做操作，将耗时从 apply 函数的 23.4 ms 提升到了 942us。

我们再来验证一下这种矢量化的方式是否进行了迭代。

```cpp
%lprun -f price price(data['AGE'], data['DIS'],data['TAX'])

"""
执行结果
Timer unit: 1e-06 s

Total time: 0.003699 s
File: <ipython-input-98-adc0838a8a0c>
Function: price at line 1

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     1                                           def price(age,distances,tax):
     2         1       3699.0   3699.0    100.0      return age **2  + distances ** 3 + tax ** 4

"""
```

从执行结果可以看出，pandas 的矢量化方式只执行了一次，执行一次的原因就是它**同时**将函数作用于整个列。由此可知，pandas 代码如果可以写得好的话，速度也是很快的。再来介绍一下什么是矢量化：pandas 的数据结构 DataFrame 和 Series 的底层都是数组，这种设计导致了所有的操作都是面向整个数组的，而非数组中单个元素。矢量化就是以整个数组而非单个元素为单位进行操作的过程。

接下来，再来看一下 numpy 的矢量化。

```cpp
%%timeit

data['distance'] = price(data['AGE'].values, data['DIS'].values,data['TAX'].values)

"""
223 µs ± 4.93 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)  
"""
```

从上面的执行结果可以看出，尽管 pandas 矢量化已经将时间缩短至了微秒级别，但 numpy 仍在此基础上缩短到了 pandas 矢量化的四分之一。numpy 之所以速度这么快，这是因为它的数组操作是运行在经过优化且编译过的 C 语言之上。

总结一下，这一部分对比了非矢量化编程与矢量化编程，直观的感受了一下两者在函数执行速度上的差异(从 229ms 提升到了 223us，提升了接近 1000 倍), 并给出了矢量化编程的定义。这几种方法的速度由快到慢依次为：

**1\. numpy 矢量化操作：没有 for 循环的 numpy 方法和函数**
**2\. pandas 矢量化操作：没有 for 循环的 Pandas 方法和函数**
**3\. apply 函数：之所以要快得多，是因为它在内部尝试遍历 Cython 迭代器**
**4\. iterrows：迭代 DataFrame 每一行，然后返回索引及 Series。将每一行构建到一个 Series 中然后访问它会很昂贵。**
**5\. 逐个元素遍历：使用 df.loc 或 df.iloc 一次访问一个单元格或行**

所以你如果真的很在意执行速度的话，优先考虑使用 numpy 矢量化。

除此之外，还对比了一下 pandas 矢量化与 numpy 矢量化，发现 numpy 要更快一些，这是为什么呢？两者又有什么区别？我们将会在下一部分讨论这些问题。