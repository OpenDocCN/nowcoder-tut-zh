# 第二章 第 2 节 pandas、numpy 矢量化编程（中）

> 原文：[`www.nowcoder.com/tutorial/10067/00f8d139265f4ba19a2f69017920ca26`](https://www.nowcoder.com/tutorial/10067/00f8d139265f4ba19a2f69017920ca26)

# 三、pandas 矢量化 VS numpy 矢量化

先来看一下 numpy 的介绍。numpy 在它的官网上有这么几句介绍：“The fundamental package for scientific computing with Python”, “NumPy brings the computational power of languages like C and Fortran to Python”。它是一个强大的科学计算库，为 python 带来了像 C 语言一样快的计算力。它如此之快，是因为建立在 C 语言这种经过优化、编译的语言之上。

从上一部分，我们可以看出，numpy 矢量化的方式要比 pandas 矢量化速度快，这是因为 numpy 要比 pandas 更处于底层的位置，而 pandas 是建立在 numpy 基础之上。

numpy 与 pandas 一样，它的函数设计也是出于操作于整个数组之上，对整个数组的数据进行快速运算的目的，而非编写循环代码。但与 pandas 的 DataFrame、Series 相比，numpy 的数组更偏底层和基础，它省去了一些功能和特性(如数据类型、数据格式化)，因此要比 pandas 的矢量化要更快一些。如果某个场景，可以使用 numpy 代替 pandas 的话，比如用不到 Series 的 index，可以选择使用 numpy 从而获得更快的速度。

上面我们对比了非矢量化和矢量化的差异以及 pandas 矢量化和 numpy 矢量化的差异，接下来我们详细介绍 pandas 矢量化编程与 numpy 矢量化编程的具体使用方法。

* * *

# 四、pandas 矢量化编程介绍

pandas 包含一系列的矢量化函数，具体如下：

*   数学运算
*   聚合操作
*   字符串函数

## 1、数学运算：

可以用在 DataFrame 和 Series 上，函数如下：

*   sum：求和

*   mean：均值

*   median：中位数

*   min：最小值

*   max：最大值

*   std：标准差

*   var：方差

*   cumsum：累积求和

*   cumprod：累积求积

*   cummax：累积最大值

*   cummin：累积最小值

相关函数演示：

```cpp
df = pd.DataFrame({'key1':np.arange(10),'key2':np.random.rand(10)*10})
"""
  key1    key2
0    0        6.980523
1    1        9.781131
2    2        7.959196
3    3        5.855253
4    4        5.907753
5    5        7.334829
6    6        9.038715
7    7        8.954209
8    8        3.576662
9    9        2.237212
"""

# 获取每列最小值
df.min()
"""
key1    0.000000
key2    0.611726
dtype: float64
"""

# 每列的中位数
df.median()
"""
key1    4.500000
key2    7.157676
dtype: float64
"""
```

## 2、聚合操作：

pandas 矢量化编程还体现在分组之后的聚合操作，具体包括以下函数：

*   count：分组中非 NA 值的数量
*   first、last：第一个和最后一个非 NA 的值
*   sum：非 NA 值的和
*   mean：非 NA 值的平均值
*   median：非 NA 值的中位数
*   std：标准差
*   var：方差
*   min、max：非 NA 值的最小值和最大值

相关函数演示：

```cpp
df = pd.DataFrame(
{'key1':['a','a','b','b','c','c','d'],
 'key2' : ['one', 'two', 'one', 'two', 'one','one','two'],
 'data1' : np.random.randn(7),
 'data2' : np.random.randn(7)
}
)
"""
  key1    key2    data1        data2
0    a         one    -0.132995    0.652716
1    a         two    0.445085    2.311643
2    b         one    0.096992    -1.277110
3    b         two    0.466623    0.358237
4    c         one    -0.327682    -0.931456
5    c         one    -0.328183    -0.651053
6    d         two    1.006750    1.852711
"""
# 按 key1 分组，然后统计各组数量
df.groupby('key1').count()
"""
      key2    data1    data2
key1            
a        2          2        2
b        2          2        2
c        2          2        2
d        1          1        1
"""
# 根据 key2 分组，然后计算 data1 列的均值
df.groupby('key1')['data1'].mean()
"""
key1
a    0.156045
b    0.281808
c   -0.327932
d    1.006750
Name: data1, dtype: float64
"""
```

## 3、字符串函数:

pandas 的一个优势在于可以非常容易、方便、高效地处理字符串，这是因为它提供了一系列针对字符串的矢量化函数。 所有的函数可以 pandas 的 Series 部分找到，链接为：[`pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.html?highlight=str#pandas.Series.str`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.html?highlight=str#pandas.Series.str)

#### (1) python 内置的字符串函数，在 pandas 中几乎都可以找到，并转变成为矢量化的，具体如下：

| 第一列 | 第二列 | 第三列 | 第四列 |
| --- | --- | --- | --- |
| len() | lower() | translate() | islower() |
| ljust() | upper() | startswith() | isupper() |
| rjust() | find() | endswith() | isnumeric() |
| center() | rfind() | isalnum() | isdecimal() |
| zfill() | index() | isalpha() | split() |
| strip() | rindex() | isdigit() | rsplit() |
| rstrip() | capitalize() | isspace() | partition() |
| lstrip() | swapcase() | istitle() | rpartition() |
|  |  |  |  |

现在来看一下，其中一些方法的使用。

```cpp
import pandas as pd

data = ['java','c','c++','js','python']
languages = pd.Series(data)

# 将首字母大写
languages.str.capitalize()
"""
0      Java
1         C
2       C++
3        Js
4    Python
dtype: object
"""
# 各个字符串长度
languages.str.len()

"""
0    4
1    1
2    3
3    2
4    6
dtype: int64
"""
# 首字母是否是‘j'
languages.str.startswith('j')

"""
0     True
1    False
2    False
3     True
4    False
dtype: bool
"""
# 将所有字母大写
languages.str.upper()

"""
0      JAVA
1         C
2       C++
3        JS
4    PYTHON
dtype: object
"""
# 字符串是否都是字母
languages.str.isalpha()

"""
0     True
1     True
2    False
3     True
4     True
dtype: bool
"""

```

#### (2) 正则表达式相关：

| 函数名称 | 介绍 |
| --- | --- |
| match | 每个字符串是否以正则表达式的匹配开始 |
| extract | 从字符串开始，返回匹配的字符串 |
| findall | 找到所有与正则表达式匹配的字符串 |
| replace | 替换符合正则表达式的字符串 |
| contains | 是否与正则表达式匹配，返回布尔值 |
| count | 统计匹配的正则表达式的个数 |
| split | 与 str.split()功能相同，但接收正则表达式 |
| rsplit | 与 str.rsplit()功能相同，但接收正则表达式 |

其中一些方法的使用：

```cpp
# 统计匹配正则表达式的个数
languages.str.count(r'^[^AEIOU].*[^aeiou]$')
"""
0    0
1    0
2    1
3    1
4    1
dtype: int64
"""

# 查找与正则表达式匹配的字符串
languages.str.findall(r'^[^AEIOU].*[^aeiou]$')
"""
0          []
1          []
2       [c++]
3        [js]
4    [python]
dtype: object
"""

# 从字符串开始位置(只查找开始位置)查找匹配正则表达式的字符串
languages.str.extract('([A-Za-z]+)')
"""
0
0    java
1    c
2    c
3    js
4    python
"""

```

#### (3) 其他字符串函数：

| 函数名称 | 介绍 |
| --- | --- |
| get | 索引操作 |
| slice | 切片操作 |
| cat | 拼接字符串 |
| repeat | 将字符串重复一定的次数 |
| normalize | 返回字符串的 Unicode 标准格式 |
| pad | 使字符串左对齐、右对齐、居中对齐 |
| wrap | 指定字符串所在行的行宽 |
| join | 将数组里的元素拼接为字符串 |

其中的一些函数介绍：

```cpp
# Series 中第一个元素与‘A’拼接，第二个与‘B'拼接，以此类推
languages.str.cat(['A', 'B', 'C', 'D','E'], sep=',')
"""

0      java,A
1         c,B
2       c++,C
3        js,D
4    python,E
dtype: object

"""
# 获取前三个字符
languages.str.slice(0,3)
"""
0    jav
1      c
2    c++
3     js
4    pyt
dtype: object
"""
# 将字符串重复一定的次数
languages.str.repeat(repeats=[1, 2, 3,4,5])
"""
0                              java
1                                cc
2                         c++c++c++
3                          jsjsjsjs
4    pythonpythonpythonpythonpython
dtype: object
"""
```