# 第二章 第 4 节 apply、applymap、map 函数介绍及本章面试高频题

> 原文：[`www.nowcoder.com/tutorial/10067/e16d819a8afe4f36a4edfba94a2e593a`](https://www.nowcoder.com/tutorial/10067/e16d819a8afe4f36a4edfba94a2e593a)

## 六、apply、applymap、map 函数介绍

通过前面的几个部分，我们已经知道 pandas 和 numpy 有非常多自带的高效矢量化函数，可以用于处理 pandas 的 DataFrame、Series，以及 numpy 的 ndarry。所以，任何时候 pandas 和 numpy 自带的矢量化函数都是最优的选择。然而，你可能会遇到 pandas、numpy 的矢量化函数不能满足需求(比如分组后要使用自定义函数)，需要自己来写代码处理，这时候优先考虑 apply、applymap、map 这三个函数。

在具体介绍这三个函数的使用方式时，先来看一下它们的区别：

**在作用对象上：**

*   apply 既可以将函数应用在 DataFrame 的行或列上，又可以用在 Series 对象

*   applymap 是将函数作用在 DataFrame 的每个元素上，如全部元素都进行加一操作

*   map 只能将函数作用在 Series 对象

    **在使用方式上：**

*   apply 与 map 函数都可以作用在 Series 对象上

*   apply 与 map 函数在接收函数作为参数时，功能是一样的

*   map 函数除了接收一个函数作为参数之外，还可以接收如字典的数据结构作为参数，起到替换值的作用。而 apply 函数不能接收类似的参数。

### 1、apply 函数的使用

apply 函数的用法按作用对象可以分为两类，第一类是作用于 DataFrame 的行或列上；第二类是作用于 Series 上。这一部分将首先介绍这两类用法，最后介绍 apply 与 lambda 的搭配使用。

**数据集**

```cpp
import pandas as pd

df = pd.DataFrame({ 'A': [1,2,3,4], 
                   'B': [10,20,30,40],
                   'C': [30,50,70,90]
                  }, 
                  index=['Row 1', 'Row 2', 'Row 3', 'Row 4'])

"""
      A     B    C
Row 1    1    10    30
Row 2    2    20    50
Row 3    3    30    70
Row 4    4    40    90
"""
```

(1) apply 函数应用于 DataFrame 的行或列上: axis 为 0 或'index'时将函数应用于列上；当 axis 为 1 或 columns 时作用于行上。

我们想新增一列 D，它的值为各行数字之和。

```cpp
# 定义求和函数，功能是计算一行数字的和
def my_sum(row):
    return row.sum()

# 将 my_sum 函数应用于每一行上，axis=1 指定作用于行上
df['D'] = df.apply(my_sum,axis=1) # 返回一个新的 Series，值为各行数字之和

"""
最终结果
      A    B      C       D
Row 1    1    10    30    41
Row 2    2    20    50    72
Row 3    3    30    70    103
Row 4    4    40    90    134
"""
```

假设我们想新增一行 Row5，值为每一列的和，这时应该怎么做？

```cpp
df.loc['Row 5'] =  df.apply(my_sum,axis=0) # axis=0,按列相加
"""
      A    B      C       D
Row 1    1    10    30    41
Row 2    2    20    50    72
Row 3    3    30    70    103
Row 4    4    40    90    134
Row 5    10    100    240    350

"""
```

(2) apply 函数作用于 Series 之上

我们想在原数据集的基础上，新增一列 D，它的值为 C 列的两倍。

这个场景只用到了 C 列的值，所以我们不能在对 DataFrame 按行或按列操作了，而是需要单独操作 C 列，就是 Series 对象。代码如下：

```cpp
def my_multiply(nums):
    return nums * 2

df['D'] = df['C'].apply(my_multiply)# 选择 C 列应用函数

"""
      A    B      C       D
Row 1    1    10    30    60
Row 2    2    20    50    100
Row 3    3    30    70    140
Row 4    4    40    90    180

"""
```

接下来看一下 apply 函数如何于 lambda 函数结合使用，顺便对比一下 lambda 函数与普通函数，体验一下 lambda 函数的简洁。案例还是用上面的三个场景，即按行累加、按列累加和 C 列乘 2。

```cpp
# 新增 D 列，它的值为各行数字之和
df['D'] = df.apply(lambda x:x.sum(),axis=1)

"""
      A     B     C     D
Row 1    1    10    30    41
Row 2    2    20    50    72
Row 3    3    30    70    103
Row 4    4    40    90    134
"""

# 新增一行 Row5，它的值为各列数字之和
df.loc['Row5'] = df.apply(lambda x:x.sum(),axis=0)

"""
      A     B     C
Row 1    1    10    30
Row 2    2    20    50
Row 3    3    30    70
Row 4    4    40    90
Row5    10    100    240

"""
# 新增一列 D，它的值为 C 列的 2 倍
df['D'] = df['C'].apply(lambda x:x*2)

""" 
     A    B      C       D
Row 1    1    10    30    60
Row 2    2    20    50    100
Row 3    3    30    70    140
Row 4    4    40    90    180
"""
```

通过上面的例子，大概可以很好的体会到 lambda 函数的简便之处，因为可能只在某一个地方进行数据的转换，其他地方可能不会用到，所以就不需要通过 def 关键字额外定义一个函数，这就是 lambda 的优势所在。

### 2、applymap 函数的使用

applymap 函数作用于 DataFrame 中的所有元素，比如说，将 DataFrame 每个元素都进行平方。

```cpp
# square 函数作用于每个元素上，即每个元素都进行平方操作
df.applymap(np.square)

"""
      A     B      C
Row 1    1    100    900
Row 2    4    400    2500
Row 3    9    900    4900
Row 4    16    1600    8100

"""
# 假设数据在计算时出现错误，每个元素需要加 1 才正确，可以进行下面的操作：
df.applymap(lambda x : x+1)
```

### 3、map 函数的使用

map 函数只可用于 Series，常规用法是做一些值的替换。map 将函数作用于 Series 中的每一个对象，然后返回转换后的 Series

(1) map 函数接收字典进行值的替换。

```cpp
# 创建一个 Serie 对象
s = pd.Series(['cat', 'dog', np.nan, 'rabbit'])

"""
0       cat
1       dog
2       NaN
3    rabbit
dtype: object
"""
# 把 cat 替换为 kitten，dog 替换为 puppy
s.map({'cat':'kitten','dog':'puppy'})

"""
rabbit 不在字典中的值将被转换为 NaN
0    kitten
1     puppy
2       NaN
3       NaN
dtype: object
"""
```

这个用法存在一个小问题，就是不在字典里的值会被替换为 NaN(如上面例子中的 Rabbit)，所以要避免这种情况发生，需要指明所有值的替换值(即使它们不需要进行替换)。这种情况下操作起来很麻烦，可以考虑使用 replace 函数。replace 函数使用方法如下：

```cpp
s.replace({'cat': 'kitten', 'dog': 'puppy'})
```

(2) map 也可以接收一个函数

```cpp
s.map('I am a {}'.format)
"""
0       I am a cat
1       I am a dog
2       I am a nan
3    I am a rabbit
"""

# 与 lambda 函数搭配使用，查看元素的数据类型
s.map(lambda x: type(x))
"""
0      <class 'str'>
1      <class 'str'>
2    <class 'float'>
3      <class 'str'>
dtype: object

"""
```

* * *

## 七、总结

本章对比了非矢量化编程与矢量化编程，直观的感受了这两种方式在速度上的差异，他们速度，从快到慢，依次为：

**1\. numpy 矢量化操作
2\. pandas 矢量化操作
3\. apply 函数
4\. iterrows 函数
5\. 逐个元素遍历**

所以，如果你非常在意速度，优先考虑使用 numpy 的矢量化编程。

接着，对比了以下 pandas 和 numpy 这两种矢量化的方式，并介绍了如何使用它们进行矢量化编程，原则就是优先使用 pandas 和 numpy 内置的各种方法；将 Python 循环转换为数组运算；将元素操作转换为 DataFrame 或 Series 操作；

最后介绍了 apply、applymap、map 函数，在矢量化编程无法满足需求时,可以作为一个优先的选择。

* * *

## 八、相关面试题

**1\. 如何理解 pandas、numpy 中的矢量化编程？**
(提示：一次性处理整个行或列，而非逐个元素遍历(或者简单理解为没有 for 循环的方法和函数)；速度快)

**2\. pandas 矢量化编程场景有哪些？**
(提示：数值计算(求和、累积求和、累积求积等)、统计相关(均值、最大值、最小值、分组后的统计)、字符串处理(比如首字母大小写、去除空格、正则表达式等))

**3\. numpy 矢量化编程场景有哪些？**
(提示：数学运算(矩阵运算；加减乘除、三角函数元素等)、数组运算(条件表达式转化为数组运算；布尔数组操作))

**4\. apply、applymap、map 三个函数有什么区别？**
(提示：作用的对象不同。apply 既可以将函数应用在 DataFrame 的行或列上，又可以用在 Series 对象；applymap 是将函数作用在 DataFrame 的每个元素上，如全部元素都进行加一操作；map 只能将函数作用在 Series 对象)

### 九、参考资料

《利用 Python 进行数据分析》

《[From Python to Numpy](https://www.labri.fr/perso/nrougier/from-python-to-numpy/)》

《Python Data Science Handbook》

[pandas 官方文档](https://pandas.pydata.org/pandas-docs/stable/reference/index.html)

[numpy 官方文档](https://numpy.org/doc/stable/reference/index.html)