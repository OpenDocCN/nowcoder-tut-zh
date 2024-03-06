# 第二章 第 3 节 pandas、numpy 矢量化编程（下）

> 原文：[`www.nowcoder.com/tutorial/10067/6ada4bab8b2c4c2d8704e9ccfaa1ee9f`](https://www.nowcoder.com/tutorial/10067/6ada4bab8b2c4c2d8704e9ccfaa1ee9f)

# 五、numpy 矢量化编程介绍

numpy 即 Numerical Python，通过 Numerical 这个词，大概就可以推断出 numpy 是针对数值计算的一个基础包。事实上，很多科学计算的包都是以 numpy 的数组 ndarray 构建的。numpy 之所以能成为科学计算的一个利器，与其中的两个原因分不开：第一个就是 ndarray 是一个具有矢量算术运算的能力快速且节省空间的多维数组；第二个就是用于对整组数据进行快速运算的标准数学函数(即矢量化，无需编写循环)

在介绍 numpy 矢量化编程具体方法时，先来感受一下 ndarray 的强大之处。分别构造一个包含一百万整数的数组，和一个等价的 Python 列表，然后将其中的元素分别乘以 2，对比所需要的时间。

```cpp
import numpy as np

my_arr = np.arange(1000000)
my_list = list(range(1000000))

%%timeit
my_arr2 = my_arr * 2
"""
839 µs ± 13.4 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
"""

%%timeit
my_list2 = [x * 2 for x in my_list]
"""
82.8 ms ± 902 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
"""
```

从上面的结果可以看出，numpy 的 ndarray 要比 python 的 list 快接近 100 倍。

numpy 提供了大量的矢量化函数来对数字数组进行数学运算(矢量化的一个重要特点是可以直接将数学公式转换为相应的程序代码)，大致分为以下三类：

**一、通用函数：一元函数、二元函数以及统计相关函数**

**二、面向数组的数组编程**

**三、矩阵运算**

完整的列表可以在[官方网站](https://docs.scipy.org/doc/numpy/reference/ufuncs.html#math-operations)查看。

### 一、通用函数

#### 1、一元函数

| 函数名称 | 介绍 |
| --- | :-- |
| np.absolute | 计算元素的绝对值。对于非复数值，可以使用更快的 np.fabs |
| np.sqrt | 计算各个元素的平方根 |
| np.square | 计算元素的平方和 |
| 三角函数：np.sin,np.cos,np.tan,np.tanh | 计算三角函数值 |
| 对数函数：np.log,np.log10,np.log2 | 对数函数 |
| np.exp | 指数函数 |

相关函数演示：

```cpp
import numpy as np

arr = np.arange(6) # array([0, 1, 2, 3, 4, 5])

# 计算平方
np.square(arr) # array([ 0,  1,  4,  9, 16, 25])

# 计算 e 的 x 次方
np.exp(arr) 
"""
array([  1\.        ,   2.71828183,   7.3890561 ,  20.08553692,
       54.59815003, 148.4131591 ])
"""
```

#### 2、二元函数：

| 函数名称 | 介绍 |
| --- | --- |
| np.multiply | 乘法运算 |
| np.divide | 除法运算 |
| np.add | 加法运算 |
| np.subtract | 减法运算 |
| np.power | 幂运算 |
| np.mod | 求模 |
| np.maximum | 最大值计算 |
| np.minimum | 最小值计算 |
| 大小比较：np.greater,np.greater_equal,np.less,np.less_equal,np.not_equal,np.equal | 执行元素级别的比较运算，最终返回一个布尔数组 |
| 逻辑运算：np.logical_and,np.logical_or,np.logical_xor,np.logical_not | 逻辑运算，包括与、或、异或、非 |

相关函数演示：

```cpp
arr1 = np.random.randn(10) 
"""
array([-1.02751919, -1.09638937,  1.13179336,  1.23127732, -0.45356249,
        0.71615982,  0.0061857 , -0.44371505,  0.40171357, -0.24611817])
"""
arr2 = np.random.randn(10)
"""
array([-0.15995116, -0.25255977,  1.4528445 ,  0.82237263, -0.21862237,
        0.28952185, -0.09066982,  0.66898357,  0.60824414,  0.46818411])
"""
# 加法操作
np.add(arr1,arr2)
"""
array([-1.18747035, -1.34894914,  2.58463786,  2.05364995, -0.67218485,
        1.00568168, -0.08448412,  0.22526852,  1.00995771,  0.22206594])
"""

np.maximum(arr1,arr2)
"""
array([-0.15995116, -0.25255977,  1.4528445 ,  1.23127732, -0.21862237,
        0.71615982,  0.0061857 ,  0.66898357,  0.60824414,  0.46818411])
"""
```

#### 3、统计相关函数

| 函数名称 | 介绍 |
| --- | --- |
| np.mean | 平均值 |
| np.median | 中位数 |
| np.std,np.var | 标准差和方差 |
| np.max,np.min | 最大值和最小值 |
| np.argmax,np.argmin | 最大值和最小值的索引 |
| np.cumsum | 所有元素的累加和 |
| np.cumprod | 所有元素的累计积 |

相关函数演示：

```cpp
arr = np.arange(6)# array([0, 1, 2, 3, 4, 5])
# 计算均值
np.mean(arr) # 2.5
# 获取最大值索引
np.argmax(arr) # 5
# 累加和
np.cumsum(arr) # array([ 0,  1,  3,  6, 10, 15])
```

### 二、面向数组的数组编程

#### 1、条件表达式转化为数组运算

​ np.where 是表达式 x if condition else y 的向量化版本

​ 比如我们想比较某个数组的元素，大于 0 时，标记为 1，小于 0 时，标记为-1，

```cpp
arr = np.random.randn(4, 4)
"""
array([[-0.64842112,  1.9479558 , -1.49693352, -0.97000238],
       [-0.17292104, -0.80421999,  0.4432314 ,  1.13129535],
       [-0.50165403, -0.6582916 ,  2.02889805, -0.05534674],
       [ 0.73472144, -0.83840932,  0.26924191, -0.8519835 ]])
"""
np.where(arr>0,1,-1)
"""
array([[-1,  1, -1, -1],
       [-1, -1,  1,  1],
       [-1, -1,  1, -1],
       [ 1, -1,  1, -1]])
"""
```

#### 2、布尔数组操作:

​ any 方法测试在数组中是否有一个或多个 True，all 方法检查是否每个值都是 True:

```cpp
bools = np.array([True, False, True, False])
bools.any() # True
bools.all() # False
```

#### 3、数组排序：

​ 使用 sort 方法原地排序

```cpp
arr = np.random.randn(4) # array([ 2.533471  , -0.17242327, -0.2078463 ,  1.43866138])
arr.sort()
# array([-0.2078463 , -0.17242327,  1.43866138,  2.533471  ])
```

### 三、矩阵运算

*   矩阵加减法
*   矩阵与数字相乘
*   矩阵与矩阵相乘(使用@操作符)
*   矩阵转置
*   矩阵 Hadamard 乘积(使用*操作符)

相关代码演示：

```cpp
A = np.array( [[1,1], [0,1]] )
B = np.array( [[2,0], [3,4]] )

# 矩阵相乘

A@B

"""
array([[5, 4],
       [3, 4]])
"""

# Hadamard 乘积
A*B

"""
array([[2, 0],
       [0, 4]])
"""
```