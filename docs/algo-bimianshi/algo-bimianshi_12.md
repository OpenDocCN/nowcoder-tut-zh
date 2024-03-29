# 第四章 第 1 节 数据清洗（上）

> 原文：[`www.nowcoder.com/tutorial/10067/b962c29967d746cf839e84582fa652cd`](https://www.nowcoder.com/tutorial/10067/b962c29967d746cf839e84582fa652cd)

# 一、写在前面

数据科学领域有一句话广为流传，即“数据科学家 80%的时间花在获取、清洗、处理数据上，只有 20%的时间用于模型构造和训练”。
可见，模型训练前的数据准备工作会占用大部分工作时间。数据清洗阶段之所以会耗时这么久，是因为它涉及的内容、涵盖的步骤非常多，是一个非常宽泛的概念。
先来看一下，数据清洗在维基百科中的定义：“数据清洗是从数据集、数据表或数据库中识别、纠正(或移除)错误或不准确的数据的一个过程，同时也是指从数据中识别不完整的、不正确的、不准确的、不相关的部分，然后对这些脏数据进行替代、修改、删除”。

上面的定义其实也非常笼统，比如说不完整是什么意思？怎么判断是错误信息？怎么样算不准确？拿什么进行替代？如何进行修改？
在本章中会一一进行解释，并给出相应的处理方式。

尽管数据清洗非常耗时，也属于脏活、累活，但我们在进行数据分析和模型训练之前，一定要优先进行数据清洗工作，因为它决定了数据的质量如何，而数据质量在很大程度上决定了模型的表现，数据和特征决定机器学习的上限，而模型和算法只是逼近这个上限，用杂乱数据训练出的模型无法输出有意义的结果。事实上，一个简单的模型如果使用质量较高的数据进行训练的话，它的表现效果可能要超过使用脏乱数据的复杂模型。
**所以，做好数据清洗工作能起到事半功倍的效果。**

除此之外，数据清洗和在此基础之上的模型训练、数据挖掘是会交织在一起循环进行的，当模型表现不好时，会再次进行数据处理,反反复复，因此非常有必要掌握这部分的内容。

本章首先会介绍高质量数据的几个标准，然后介绍数据探索，数据探索的目的是了解数据，然后在了解数据的过程中发现不符合标准的数据，为后面的处理作准备；接下来介绍数据清洗常见的几个场景，会涉及识别方式和处理方式。

* * *

# 二、数据标准

下面介绍一下几个标准，可以作为我们在清理数据时的一个准则或方向。

1.  完整：数据要尽可能保证完整。但缺失是不可避免的，要知道每个特征的缺失情况，并采取合适的方式(如删除或填充)对其进行处理。
2.  一致：同一份数据在数据集中应保持一致，不可自相矛盾。比如同一个人的性别要么为男，要么为女，两者之中只有一个是正确的，一个人不能同时出现两种性别。
3.  统一：数据应该使用统一的计量单位，比如身高的单位可能是米，也可能是厘米，这个时候应使用统一的单位。
4.  数据类型：每一列数据都要有一个明确的数据类型，比如数值类型、布尔类型、日期类型。在机器学习中，这些类型可能都需要转换到数值类型。
5.  数值范围：一些连续型数据可能有一定的取值范围，比如年龄、身高；对于离散型变量，也有取值限制，比如性别，只能为男或女。
6.  数据格式：有些数据的形式要符合一定的格式，比如日期的格式可能为‘YYYY-mm-dd’，这时候要把其他形式的日期转换为这种统一的格式。
7.  跨字段验证：一些字段之间存在着逻辑关系，比如大学入学时间要晚于高中毕业时间。

上面只是简单的提到了几个标准，完整的列表你可以在[维基百科](https://en.wikipedia.org/wiki/Data_cleansing#Data_quality)里找到。

* * *

# 三、数据集介绍

数据集使用的是 kaggle 平台的 Titanic，你可以在[这里](https://www.kaggle.com/c/titanic/data)看到有关这个数据集和各个字段的介绍。

* * *

# 四、数据探索

数据探索是对数据进行了解的一个过程，同时在探索过程中识别出数据中存在的一些问题，如不完整、不正确、不相关的问题，然后在此基础上，对数据进行清洗。

## 1、非结构化数据转结构化

在拿到数据后，首先要判断数据是结构化的，还是非结构化的。如果数据为非结构化，需要将转换为结构化，才可以在此基础上进行处理。那么结构化和非结构化又分别是什么呢？

结构化数据是以行、列的形式组织的表格，常见的如 CSV、数据库表记录等，行为观察值，列是特征。

非结构化数据是一团数据,所有的数据糅合在一起，作为一个特征存在，比较常见的是文本格式，比如服务器日志。

可以通过 pandas 的一些方法，将糅合在一起的特征，拆分成一个个特征值(在处理时，要注意第一行是标题还是数据，否则可能数据成了第一行，这样就少了一行数据)，这样就可以对数据集进行探索了。

下面通过一个具体的例子来理解非结构化数据到结构化数据的转换。

日志数据(将其保存为 log.txt)：

步骤 1 开始

步骤 1: : 任务 1 : Followed link after success : 开始执行任务 (2019/05/10 02:00:22.177)

步骤 1: : 任务 1 : [nr=6, errors=0, exit_status=0, result=true] : 任务执行完毕 (2019/05/10 02:00:45.227)

步骤 1: : 任务 2 : Followed link after success : 开始执行任务 (2019/05/10 02:00:45.227)

步骤 1: : 任务 2 : [nr=6, errors=0, exit_status=0, result=true] : 任务执行完毕 (2019/05/10 02:09:21.490)

步骤 2: : START : Start of job entry : 开始执行任务 (2019/05/10 02:10:26.177)

步骤 2: : START : [nr=7, errors=0, exit_status=0, result=true] : 任务执行完毕 (2019/05/10 02:10:26.179)

步骤 2: : 任务 5 : Followed 无条件的链接 : 开始执行任务 (2019/05/10 02:10:26.179)

步骤 2: : 任务 5 : [nr=7, errors=0, exit_status=0, result=true] : 任务执行完毕 (2019/05/10 02:17:51.991)

从上面的日志记录可以看出，步骤名称、任务名称、任务状态以及执行时间都糅合在一行之中，因此需要将其拆分开来，以便于分析。

```cpp
import pandas as pd

log_path ='./log.txt'
log_list = []

data = pd.read_table('./log.txt',skiprows=1,names=['log_texg'])

```

上述代码的结果如下：

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164741/0081Kckwly1gmczg1yjk4j30lq0eognq.jpg`](img/78db5a53ca55ca19a1b6f50bdadf5736.png)

```cpp
核心字段使用“:”进行标记，可以用它作为分隔符。代码如下：
df['log_text'].str.split(': ',expand = True) # expand 参数指定将字符串分割成独立的列
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164830/0081Kckwly1gmd2c8ja1bj30xk0emn0j.jpg`](img/9ad4a3339170239dfb684db749b8dd35.png)

接下来进行列名的重命名，便于识别每一列。

```cpp
df.columns = ['step','c1','task','c2','status']
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164769/0081Kckwly1gmd2egk0icj30xq0ey77p.jpg`](img/a38482612e9836e2551ff73f1edad131.png)

截止到目前，数据基本是有结构的了。你也可以继续对 status 这一列进行拆分，这部分操作就不具体介绍了。

## 2、数据概览

可以了解数据集的基本概括，比如数据集共有多少条记录，存储量有多大，有哪些特征，各个特征的数据类型是什么、缺失值的情况以及最大值、最小值、均值等统计信息。

读取数据

```cpp
import numpy as np 
import pandas as pd

train_data = pd.read_csv('/kaggle/input/titanic/train.csv')
```

查看特征的数据类型、非 null 值数量以及内存占用情况。

```cpp
train_data.info()

"""

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   PassengerId  891 non-null    int64  
 1   Survived     891 non-null    int64  
 2   Pclass       891 non-null    int64  
 3   Name         891 non-null    object 
 4   Sex          891 non-null    object 
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64  
 7   Parch        891 non-null    int64  
 8   Ticket       891 non-null    object 
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    object 
 11  Embarked     889 non-null    object 
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB

"""
```

上面的 info()函数告诉我们了以下几个信息：

*   共有 891 条记录(RangeIndex: 891 entries, 0 to 890)

*   共有 12 个特征以及各个特征的数据类型(Dtype)，其中属于 float64 有两个, int64 的有五个, object 类型的有五个。这里的 object 类型基本都是字符串，在数据清洗时，需要将其转换为数值类型。

*   每个特征下非 null 值的记录数量，可以发现，Cabin 这一特征非 null 只有 204 个，缺失值较多，需要进行缺失值的处理；Age 列缺失了 177 个数据，Embarked 列缺失两个数据。

*   内存(memory usage)占用了 83.7 kb

    数据集的统计描述信息

```cpp
train_data.describe() # 默认展示数值类型的特征
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164825/0081Kckwly1glhphjipv6j30xo0eedhp.jpg`](img/02feff8f39324c3363885cd3e12706c6.png)

describe 函数提供了如下几个统计信息:

*   count: 各个特征的非 null 值数量

*   mean、std、min、max 分别表示均值、方差、最小值和最大值。我们可以通过最小值和最大值来检测是否存在异常数据。比如说假设 Age 列的最小年龄(min)为-5，-5 就是异常值，最大年龄(max)为 200，200 就是异常值；

*   min、25%、50%、75%、max 这些值组成了四分位数，可以通过它了解大体数据的分布情况，尽管不是很形象。

    describe 默认只展示数值类型的特征，假设我们想获取其他类型的信息，可以向 describe 函数传递 include 这个参数，并将其设置为'all'。代码如下：

```cpp
train_data.describe(include='all')
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164804/0081Kckwly1glhq4pie4kj31i40jewhv.jpg`](img/5de100f8617c8f1ef408e578f06cc495.png)

对比上面的两张截图，你会发现，第二张截图中新增加了几个特征(Name、Sex、Ticket、Cabin、Embarked)以及一些信息(unique、top、frep)，这些新增的信息只有对非数值类型的特征才有意义。接下来介绍一下这几个新增加的信息。

*   unique：唯一值的个数。比如 Sex 只能取男和女，因此只有两个。而 name 一列为 891，说明有 891 个不同的姓名。当这个数量与你自己判断的不一致时，要特别注意。
*   top：会选取出现出现次数最多(众数)的那个值，假设有多个数值出现次数最多，那么会从中任意选取一个；而 freq 表示这个众数出现的次数。

## 3、可视化

通过数据可视化，可以发现一些异常值，比如说通过直方图，观察数据的分布，来发现是否有离群值的存在；通过特征之间的相关性，筛选与目标值相关的特征。

在介绍具体的可视化技术之间，先来看一下数据的几种分类，因为不同的图表适用于不同类型的数据，了解一个数据的测量方式是选择正确统计分析方法的前提。

数据大致分为两类，第一类是定量数据，其本质是数值，是衡量某样东西的数量；第二类是定性数据，本质上是类别, 是描述某样东西的性质。比如年龄是定量，而性别是定性。

这两类数据可以继续细分，定性可以细分为定类和定序；定量可以分为定距和定比。

*   定类：这类数据只按名称进行分类，比如人的姓名、血型(A、B、AB 和 O 型)、邮政编码、眼睛的的颜色、种族，这类数据是没有顺序的；对这类数据，我们可以进行的操作是统计每类值出现的次数；在统计值的基础上，可以绘制饼图和条形图。我们不可以对这类数据进行数值的计算，比如取平均值、中位数和方差，这些都是无意义的。

*   定序：是继承了定类的属性，并进行了扩展。定序是可以进行排序的，可以理解为其中的某些数据要比其他数据要好。像考试成绩(A、B、C、D)、座舱等级(一等座、二等座)、收入等级(低收入、中等收入、高收入)、受教育程度(高中、本科、研究生、博士)都属于这类数据。这类数据我们可以进行排序和比较，因此可以计算中位数和百分位数，进而绘制箱线图。

*   定距：关心的是数值，并且数值可以排序，数值之间的差异也是有意义的，我们可以对数值进行排序和比较,也可以进行加减运算。属于这类数据的例子有 Ph 值、温度。我们可以对其进行众数、中位数的统计，并可以计算均值、标准差；可以绘制条形图、饼图、箱线图和直方图

*   定比：与定距相比，拥有定距的所有特性；除此之外，对“零”有着清晰的定义，“零”意味着什么都没有。像这样的例子包括长度、宽度、反应速率、脉搏。定比类数据除了进行加减运算外，还可以进行乘除运算。此外，还可以统计众数、中位数，及计算标准差和方差；可以绘制箱线图和直方图。定距与定比的差异主要是在“零”有没有意义。举个例子，重量是定比类，因此除法是有意义的，我们可以说 4 克是 2 克的两倍，这时候的零无意义；但我们不能说温度 4 摄氏度是 2 摄氏度的两倍，因为这时候的零是有意义的，零摄氏度转换为华氏度，是 32 华氏度，2 摄氏度等于 35.6 华氏度，4 摄氏度等于 39.2 华氏度。

    常用的可视化工具有两种，一个是 Matplotlib，一个是 seaborn，两者的区别如下：

    Matplotlib 是 python 绘图的一个类库，它可以与 numpy、pandas 结合使用来可视化数据。它可以用来绘制各种统计图表，提供了基本的绘图功能。

    seaborn 是建立在 Matplotlib 基础之上的 python 类库，也可以理解为是 Matplotlib 的一个子集。它在 Matplotlib 的基础上扩展了很多功能，比如说单变量和双变量数据的可视化、线性回归模型的可视化以及时间序列数据的可视化。除此之外，它还提供了多个漂亮的主题，对字体、配色都进行了优化，提升了 Matplotlib 的美感。

    Matplotlib 的语法比较复杂，参数较多，所以比较难学习；而 seaborn 语法简单，非常容易理解和使用，可以先学习 seaborn，有一定基础之后，再学习 Matplotlib。

    Matplotlib 参数较多，可配置性较高；而 seaborn 默认了一些主题，省去了配置一些参数的步骤。

    可视化图表根据使用场景的不同，可以分为相关类图表、分布类图表、变化趋势类图表和组成(部分与整体)类图表。下面会依次对这几种分类做一个大致的介绍。

    相关性图表主要用来展示两组或多组数据间的相互关系，或者说是一组数据是随着其他组数据是如何变化的，常见主要有散点图、热力图(heatmap)以及成对图(pairplot)。

    分布类图表主要展现的是数据分布情况，比如各个数据值出现的频数、概率密度、累积分布，包括直方图、密度图和箱线图。

    变化趋势类图表主要展示数据随时间变化的一个情况，趋势图常见的主要有折线图。

    组成(部分与整体)类图表，展示整体的组成情况，或者是每个部分占总体的百分比。组成类图表常用的是饼图和柱状图。

    这些图表可以使用前面提到的 Matplotlib 和 seaborn 进行绘制，具体绘制方式在此进行介绍，请读者自行探索。

    在了解了这些内容之后，我们进行一下实践，使用箱线图查看一下泰坦尼克乘客年龄的分布情况。

    箱线图可以在一个图表中展示多种数据，具体包括最小值、最大值、均值、上下四分位数 (四分位数也称四分位点，是指在统计学中把所有数值由小到大排列并分成四等份，处于三个分割点位置的数值。中间的四分位数就是中位数，处在 25%位置上的数值（称为下四分位数，Q1）和处在 75%位置上的数值（称为上四分位数，Q3)，它是一种可以观察数据分布状况(如数据差异性、离散程度和异常值)的图表。

    seaborn 使用 boxplot 函数进行绘制，代码如下：

```cpp
import seaborn as sns

# 使用泰坦尼克数据集，对年龄这一特征绘制箱线图
sns.boxplot(x=train_data['Age'])
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164838/0081Kckwly1gkyy7gs4ryj30ke0figm4.jpg`](img/9c5d9ad6003f1beed0191c9040d99f67.png)

从这个箱线图，我们可以得出这么几个结论：最小为 0 岁左右；最大值为 65 岁左右；均值在 28、29 岁左右；Q1 在 20 岁左右；Q3 在 39 岁左右；重要的是我们知道了数据存在离群值，离群值介于 65-80 之间，因此需要对离群值进行处理，这部分在数据清洗阶段进行介绍。

接下来介绍一下热力图，它可以展示特征间的相关程度，便于我们筛选特征。要知道，特征和属性通常有明显的区分。属性一般是表格数据的列,特征则一般只指代对机器学习算法有益的属性。也就是说,某些属性对机器学习系统不一定有益,甚至有害。例如,当预测二手车下次维修的时间时,车的颜色应该不会对预测有什么帮助。所以，非常有必要识别出有益的特征。热力图也可以称为 Correllogram(关联图)，是一种针对多元变量进行分析的图表，它将数据之间的相关程度可视化为一个带颜色和数值的矩阵，数值越大，矩阵颜色越深,代表相关性越高；当值为负时，表明为负相关。热力图可以快速画出数据间的相关系数，可以快速得知我们关心的特征与哪些数据相关。Pandas 的.[corr](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.corr.html?highlight=corr#pandas.DataFrame.corr)()方法会为所有的列计算皮尔逊相关系数，使用 seaborn 的 heatmap 函数进行可视化。

```cpp
sns.heatmap(train_data.corr(), 
            annot=True,
            cmap='RdYlGn',
            linewidths=0.2)
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612168164849/0081Kckwly1gky05xy7bij319f0u0jxm.jpg`](img/16617e464b41217aa1037b3954411194.png)

也可以只展示目标变量与特征之间的相关性，代码如下：

```cpp
train_data.corr()['Survived']

"""

PassengerId   -0.005007
Survived       1.000000
Pclass        -0.338481
Age           -0.077221
SibSp         -0.035322
Parch          0.081629
Fare           0.257307
Name: Survived, dtype: float64

"""
```

观察上图可以得出这么几个结论：

*   对角线颜色最深，且值为 1(最大值为 1，最小值为-1)，这其实是每个特征与自己本身的相关度。
*   我们的目标变量是 Survived(存活状况)，它与两个特征相关程度较高：一个是与 pclass(相关系数为-0.34；pclass 代表座位的级别，也就是头等舱、一等座、二等座这种概念)，另一个是 Fare(相关系数 0.26，Fare 表示票价)。PassengerId 与 Survived 的相关性只有 0.005，说明与存活率的相关程度不大，因此可以将该特征删除。
*   我们可以为相关系数定义一个阈值，只选取超过阈值的特征来进行模型训练。比如，阈值定义为 0.2(表示相关系数要么大于 0.2，要么小于-0.2), 代码如下：

```cpp
train_data.corr()['Survived'].abs()>.2 
"""
PassengerId    False
Survived        True
Pclass          True
Age            False
SibSp          False
Parch          False
Fare            True
Name: Survived, dtype: bool
"""

```

阈值的确定要通过模型的表现来确定。我们可以定义多个阈值，比如 0.1，0.2，0.3 和 0.4，然后对比几种阈值对应的模型表现，然后选择表现最好阈值。

* * *