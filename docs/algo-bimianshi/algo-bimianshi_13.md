# 第四章 第 2 节 数据清洗及常见面试题总结（下）

> 原文：[`www.nowcoder.com/tutorial/10067/fccdb7f57f434cc1adac331185facebd`](https://www.nowcoder.com/tutorial/10067/fccdb7f57f434cc1adac331185facebd)

# 五、数据清洗

数据清洗主要包括以下几个场景，在进行清洗时，可以根据这几个方面对数据进行检测和处理：

**1\. 缺失值处理
2\. 离群值处理
3\. 重复值处理
4\. 数据类型转换
5\. 删除不需要的特征
6\. 格式不一致
7\. 字符串相关处理
8\. 列名重命名
9\. 日期和时间类型处理
10\. 非 ASCII 字符处理
11\. 数据的单位不统一**

### 1、对于缺失值的识别与处理

上一章已经详细的介绍过，在此不再赘述，你可以点击[这里](https://blog.nowcoder.net/n/6b0c7721a7ac4c36892c33d467dfb38e)进行查看。

### 2、离群值处理

离群值，显著不同于其他数据，与其他数据分布有较为显著的不同。有时也称非离群点为“正常数据”，离群点为“异常数据”。

离群值与其他数据非常的不同，它的存在会扭曲对数据的认知。比如，当普通白领的工资与世界首富的工资进行平均后，会发现白领的工资被拉高了很多。因此，需要对离群值进行处理。

在处理之前，首先要对离群值进行识别。对于数值类型的数据，我们可以使用直方图、箱线图进行识别(比如之前提到的，使用箱线图查看乘客年龄分布情况)；

除此之外，还可以使用 describe 函数生成的统计描述信息进行识别。如下图，Age 这一列的数据 75%的都在 38 岁左右，但最大值为 80 岁，因此很明显存在离群值。

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164825/0081Kckwly1glhphjipv6j30xo0eedhp.jpg`](img/02feff8f39324c3363885cd3e12706c6.png)

对于分类数据，可以使用条形图识别。我们使用条形图来展示以下泰坦尼克每种座舱的人数分布情况，如下：

```cpp
sns.countplot(x="Pclass", data=train_data)
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168835664/0081Kckwly1glin9dzfs4j30og0e8aar.jpg`](img/ddcd956f1e3b6993ffef11719aa879ff.png)

从上图可以看出，Pclass 这个特征没有离群值。出于展示的目的，我手动构造一个离群值，将某一行记录里的 Pclass 列改为‘4’。一般来说，若某个分类下只有一两个值，或者分类名称为 others，这时可以将其认为是离群值。展示如下：

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168835573/0081Kckwly1glt0rm7tioj30nw0ey3za.jpg`](img/562f72653d91e7b716f14b664177f37a.png)

此外，还可以使用 3σ准则进行识别。如果样本是正态分布或近似正态分布，可以考虑使用 3σ方法，认为 99%以上的数据集中在均值上下 3 个标准差的范围内。具体来说，数值分布在（μ-3σ,μ+3σ)中的概率为 99.73%，超过这个范围的极大或极小值，那就是异常值了。如果数据不服从正态分布，也可以用远离平均值的多少倍标准差来描述。

在识别离群值后，有以下几种方法来处理离群值：

*   离群值数量较少可以直接删除。

*   使用均值、中位数或众数来替代。

*   将异常值当作缺失值来处理。

*   不处理——如果算法对异常值不敏感则可以不处理；但如果算法对异常值敏感,则需要对其进行处理,如基于距离计算的一些算法,包括 k-means,knn 等。

    接下来简单演示一下删除离群值，代码如下：

```cpp
# 获取最大 Age 的索引
max_age_index = train_data['Age'].argmax()

train_data.drop(max_age_index,inplace=True)
```

### 3、重复值处理

重复值是指有多行数据是相同的。重复值出现的原因有多种，比如用户提交了两次表单，而数据库层面没有进行唯一性校验；数据由不同数据源合并而来；

重复值的识别可以使用 pandas 的 duplicated 方法，这个方法返回一个布尔型 Series，表示各行是否是重复行。

```cpp
train_data.duplicated()

"""
泰坦尼克数据集中不存在重复的数据
0      False
1      False
2      False
3      False
4      False
       ...  
886    False
887    False
888    False
889    False
890    False
Length: 891, dtype: bool
"""

# 构造一个带有重复值的 DataFrame
data = pd.DataFrame({'k1': ['one', 'two'] * 3 + ['two'],'k2': [1, 1, 2, 3, 3, 4, 4]})
"""
  k1    k2
0    one    1
1    two    1
2    one    2
3    two    3
4    one    3
5    two    4
6    two    4
"""
# 最后一行数据为重复行
df.duplicated()
"""
0    False
1    False
2    False
3    False
4    False
5    False
6     True
"""
```

对于重复值，可以进行删除,使用 pandas 的 drop_duplicates 函数进行删除,drop_duplicates 默认保留的是第一个出现的数据。传入 keep=’last’则保留最后一个，代码如下所示。

```cpp
df.drop_duplicates()

"""
  k1    k2
0    one    1
1    two    1
2    one    2
3    two    3
4    one    3
5    two    4
"""
```

重复值除了上面介绍的重复记录这一种之外，还有一种是特征重复。特征重复可以理解为两特征之间的相似度非常高，比如为 1，因此需要将其中的一个删除。pandas 的 corr 方法可以计算特征的相似度。该方法默认使用皮尔逊相关系数，可以使用 method 参数来指定肯德尔或斯皮尔曼相关系数，代码如下：

```cpp
train_data.corr() # 该方法只能对数值型重复特征去重
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168835666/0081Kckwly1gmd3gviqw7j30wc0dcq52.jpg`](img/b715cb8d807f2944418e50f9ac375a14.png)

### 4、数据类型转换

进行模型训练的数据一般是数值类型，因此需要将字符串类型的数字、分类等非数值类型的数据转换为数值类型。

泰坦尼克乘客的性别为 female、male，需要将其转换为数字，代码如下：

```cpp
# 可以使用 map 函数手动进行映射
train_data['Sex'].map({'male':0,'female':1})

# 也可以使用 Categorical
train_data['Sex'] = pd.Categorical(train_data.Sex).codes

"""
0      0
1      1
2      1
3      1
4      0
      ..
886    0
887    1
888    1
889    0
890    0
Name: Sex, Length: 891, dtype: int64
"""
```

我们也可以使用 scikit-learn 的 LabelEncoder 进行转换。 LabelEncoder 会将分类值转换为 0,1,2...，代码如下：

```cpp
from sklearn.preprocessing import LabelEncoder

encoder=LabelEncoder()

encoder.fit_transform(train_data['Sex'])
```

对于字符串类型的数字，可以使用 astype 函数将其转换为数值类型，代码如下：

```cpp
df[col_int] = df[col_int].astype('int')
df[col_float] = df[col_float].astype('float')
```

### 5、删除不需要的特征

```cpp
特征存在的意义在于为我们的预测目标提供信息，有些特征与我们要预测的目标无关，因此这些特征就没有了价值，这时候可以直接删除掉。这类信息可以分为两种，第一种是不相关，这一类比较好理解，比如一个人的姓名、身份证号与 TA 是否得某种病是无关的；第二种是无信息，即特征不能提供信息，具体表现为所有样本在这些特征上的取值在很大程度上(比如 95%以上)都是相同的。举个例子，假设泰坦尼克数据集有个特征为乘客国籍，若 95%以上的乘客都来自同一个国家，那么国籍这一特征就提供的信息就比较有限。当然，即使特征取值在很大程度上都相同，也要逐个进行判断，来看它是提供了有效信息。

对于第一种不相关的特征，可以直接删除掉。比如泰坦尼克乘客的姓名不能决定 TA 是否存活，这时候认为姓名这个特征与我们的目标值是否存活是没有关系的，这时候可以使用 drop 方法将其删除掉。代码如下：    
# axis=1 指定删除列
train_data.drop('Name',axis=1,inplace=True)
```

对于第二种特征，首先要统计该特征下每种取值的个数，然后计算百分比，若超过了 95%，再对其是否提供了有效信息进行判断，相关代码如下：

```cpp
num_rows = len(df.index)
low_information_cols = [] 

for col in df.columns:
    cnts = df[col].value_counts(dropna=False)
    top_pct = (cnts/num_rows).iloc[0]

    if top_pct > 0.95:
        low_information_cols.append(col)
        print('{0}: {1:.5f}%'.format(col, top_pct*100))
        print(cnts)
        print()
```

### 6、格式不一致

首字母大小写不一致。常见的一类错误就是，属于分类类型的特征，首字母大小写不一致，然后转换为数字时，将同一个分类转换成了不同的数字。比如，乘客性别可能为 female、Female、male、Male，其实只有两种类型，本可以用两个数字来表示，但却转换成了四个数字。对于这种情况，首先统计有几个不同的分类值，若存在大小写的问题，则需要进行大小写转换。代码如下：

```cpp
train_data['Sex'].value_counts(dropna=False)

"""
male      577
female    314
Name: Sex, dtype: int64
"""

# 这里首字母都是小写，出于演示的目的，将它们都转换为大写

train_data['Sex'] = train_data['Sex'].str.capitalize()

"""
Male      577
Female    314
Name: Sex, dtype: int64
"""
```

### 7、字符串相关处理

字符串中有不该存在的字符，比如空格、特殊符号、字符串数据后面有一个回车符，因此需要对其进行处理。pandas 中有大量的字符串处理函数，在此不一一介绍，只介绍几种常见的场景。

(1) 移除空格。代码如下：

```cpp
df[col] = df[col].str.lstrip() # 删除字符串开头的空格

df[col] = df[col].str.rstrip() # 删除字符串结尾的空格

df[col] = df[col].str.strip() # 删除字符串左右两边的空格，当中的空格仍在

df[col] = df[col].str.replace(' ','')  # 用 replace 可以替换所有的空格
```

(2) 删除字符串中的特殊字符，代码如下：

```cpp
df['col_1'].replace('\\n', '', regex=True, inplace=True)

df['col_1'].replace(' &#.*', '', regex=True, inplace=True)
```

### 8、列名重命名

之所以要进行列名重命名，主要有两个原因，第一个是列名太长、太复杂，在使用这些列名时不太方便；第二个是数据集中可能有相同的列名，或含义相同的两个列名，为避免干扰分析结果，则需要针对某一个数据列的列名进行重命名。可以使用 rename 函数进行重命名，代码如下：

```cpp
data = pd.DataFrame(np.arange(12).reshape((3, 4)),columns=['one', 'two', 'three', 'four'])

"""

  one    two    three    four
0     0    1       2       3
1     4    5       6       7
2     8    9       10       11

"""
# 将列名进行大写
data.rename(columns=str.upper)
```

### 9、日期和时间类型处理

在处理时间序列数据时，我们可能会遇到字符串格式或时间戳格式的时间表示。这就要求我们将这些格式的数据转换为指定的日期(datetime)格式，以便使用这些数据进行有意义的分析和展示。

首先，介绍如何将字符串转换为日期格式。

```cpp
df['timestamp_dt'] = pd.to_datetime(df['stringtime'], format='%Y-%m-%d')

df['year'] = df['timestamp_dt'].year # 获取年份
df['month'] = df['timestamp_dt'].month # 获取月份
df['weekday'] = df['timestamp_dt'].weekday # 获取星期
```

接下来，介绍如何将时间戳转换为日期格式，代码如下：

```cpp
dt = pd.to_datetime(1490195805, unit='s') # 时间精确到秒
"""
Timestamp('2017-03-22 15:16:45')
"""
dt.year # 获取年份
dt.month # 获取月份
dt.day # 获取天数
```

### 10、非 ASCII 字符处理

有些列中存在非 ASCII 的字符。我们可以采用删除或者替换的方式来解决非 ASCII 问题，代码如下：

```cpp
df['columnname'].replace({r'[^\\x00-\\x7F]+':''}, regex=True, inplace=True)
```

### 11、数据的单位不统一

有些特殊的列，比如身高、体重，可能带有计量单位，这时候可能会出现单位不一致的情况。比如，就身高而言，有的记录是 m，有的是 cm；就体重而言，有的单位是千克(kgs),有的单位是磅(lbs) ，这时候需要将其进行统一。方法也比较简单，即单位的转换，在此不进行赘述。

* * *

# 六、相关面试题

**1\. 如何理解数据清理**
提示：数据清理也称为数据清理，用于识别和消除数据中的错误和不一致性，以提高数据的质量

**2\. data profiling 是什么？**
提示：是对数据集的概览，它提供各种属性的信息，如值范围、离散值及其频率、空值的出现、数据类型、长度等

**3\. 一般从哪几个方面进行数据清理？**
提示：缺失值、重复值、离群值、格式不一致、类型转换、不相关特征

**4\. 如何识别离群值？**
提示：箱线图、直方图、条形图、3σ准则、四分位数

**5\. 如何处理异常值？**
提示：删除；用平均值、中位数或众数替代；有些算法不需要处理

**6\. 为什么需要进行归一化？**
提示：归一化后加快了梯度下降求最优解的速度；归一化有可能提高精度

**7\. 归一化常用的方式有哪些？各有什么特点？**
提示：有 min-max 标准化(线性变换，数据落在(0,1)之间)，这种方法有一个缺陷就是当有新数据加入时，可能导致 max 和 min 的变化，需要重新定义。z-score 标准化(处理后服从标准正态分布)，方法适用于属性 A 的最大值和最小值未知的情况，或有超出取值范围的离群数据的情况。该种归一化方式要求原始数据的分布可以近似为高斯分布，否则归一化的效果会变得很糟糕

**8\. 如何处理缺失值？**
提示：连续型：平均数或中位数数；离散型：考虑众数

**9\. 你会如何进行探索性数据分析(EDA)？**
数据集概览；数据可视化；缺失值；异常值；重复值；类型转换；是否构建新特征

* * *

# 七、总结

这篇文章开始时给出了数据清理的概念，并介绍了数据清理的必要性和重要性；接着介绍了高质量数据的标准，作为数据清理的方向和原则；接下来介绍了数据探索，包括通过探索性数据分析、描述统计以及数据可视化；最后给出了数据清洗的一般思路，即先识别再处理，并介绍了几种常见的场景。

* * *

# 八、参考资料

*   [data cleaning](https://en.wikipedia.org/wiki/Data_cleansing#Motivation)
*   《利用 python 进行数据分析》
*   《精通特征工程》
*   [利用 Pandas 分析日志数据](https://blog.csdn.net/BF02jgtRS00XKtCx/article/details/100012326)