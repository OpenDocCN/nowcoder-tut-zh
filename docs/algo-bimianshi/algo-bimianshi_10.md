# 第三章 第 2 节 实战及本章高频面试题

> 原文：[`www.nowcoder.com/tutorial/10067/f2b6a08531994f8e92814155686b8399`](https://www.nowcoder.com/tutorial/10067/f2b6a08531994f8e92814155686b8399)

# 六、缺失值处理实战

这部分的主要内容是运用 pandas 对上一部分介绍的几种方式进行实战，最后会介绍 scikit-learn 里面的相关类作为一个扩展。

数据集使用的是 kaggle 平台泰坦尼克比赛的数据集，链接是[`www.kaggle.com/c/titanic`](https://www.kaggle.com/c/titanic)

在开始写代码之前，我们可以先问自己几个问题：

**1\. 现在的特征有哪些？
2\. 特征的数据类型是什么？比如 int, float, string, boolean 等
3\. 缺失值是否是 pandas 的标准缺失值？
4\. 是否有隐藏类型的缺失值？
5\. 含有缺失值的特征是离散性还是连续型？
6\. 缺失值所占比重有多大？要删除还是填充？
7\. 你所用的机器学习模型是否对缺失值敏感？是否要对缺失值进行特殊处理？**

读取数据：

```cpp
import pandas as pd

train_data = pd.read_csv('/kaggle/input/titanic/train.csv')
```

各个特征值的缺失值情况：

```cpp
train_data.isnull().sum()

“”“
运行结果
PassengerId      0
Survived         0
Pclass           0
Name             0
Sex              0
Age            177
SibSp            0
Parch            0
Ticket           0
Fare             0
Cabin          687
Embarked         2
dtype: int64

”“”
# 选择非
data_selected = data[['Rooms','Bedroom2', 'Bathroom', 'Car', 'Landsize', 'Lattitude', 'Longtitude', 'Propertycount', 'BuildingArea']]
```

**删除含有缺失值的行**

*   删除含有缺失值的所有行
*   删除全部为缺失值的那些行
*   删除缺失值数量超过 N 的那些行
*   删除含有缺失值的列

```cpp
# 删除含有缺失值的所有行
cleaned = train_data.dropna()

cleaned.isnull().sum()  # 再次汇总各个特征值缺失值情况

"""
各个特征值已经没有缺失值了
PassengerId    0
Survived       0
Pclass         0
Name           0
Sex            0
Age            0
SibSp          0
Parch          0
Ticket         0
Fare           0
Cabin          0
Embarked       0
dtype: int64
"""

# 传入 how=’all’将只丢弃全为 NA 的那些行
cleaned = train_data.dropna(how='all')

# 删除缺失值超过两个的那些行
cleaned = train_data.dropna(thresh=2)

# 删除列，传入 axis=1
cleaned = train_data.dropna(axis=1, how='all')
```

**对缺失值进行填充**

先来看一下，如何填充 Age 列。Age 可以使用均值和中位数进行填充，而决定可以使用均值的前提条件是没有离群值。先来看一下，是否存在离群值，方法是看一下年龄值的分布情况。

```cpp
import matplotlib.pyplot as plt
import seaborn as sns

sns.countplot(x='Age',data=train_data)
fig=plt.gcf()
fig.set_size_inches(18,12)
plt.show()
# 通过年龄分布图来看，是存在离群值的，最小年龄为 0.5，而最高年龄为 80
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612167334993/0081Kckwly1gkke5ap10xj30wg0jd74f.jpg`](img/c26d0db7227ca8b8ba860cf4e5f32b98.png)

```cpp
# 对比以下 Age 列的均值和中位数,发现两者差不多，一个是 29，一个是 28
mean_age = train_data['Age'].mean() # 获取 Age 列的均值
median_age = train_data['Age'].median() # 获取 Age 列的中位数
mode_age = train_data['Age'].mode() # 获取 Age 列的众数
# 使用中位数进行填充
train_data['Age'].fillna(median_age)
```

对 Embarked 的填充。Embarked 表示登船的港口，是离散值，不能使用均值或中位数进行填充，考虑使用众数进行填充

```cpp
# 统计各个港口的登陆人数
train_data['Embarked'].value_counts()

"""
S    644
C    168
Q     77
Name: Embarked, dtype: int64
"""
# S 港口登入人数最多，使用 S 填充

train_data['Embarked'].fillna('S')
# 使用缺失值的前一个值进行填充
train_data['Embarked'].fillna(method='ffill')

# 使用缺失值的后一个值进行填充
train_data['Embarked'].fillna(method='bfill')
```

**使用回归或分类模型来预测缺失值并进行填充**。

数据集使用的是 kaggle 平台的 Melbourne Housing 数据集，链接为：[`www.kaggle.com/anthonypino/melbourne-housing-market`](https://www.kaggle.com/anthonypino/melbourne-housing-market)

```cpp
import pandas as pd

data = pd.read_csv('../input/melbourne-housing-market/Melbourne_housing_FULL.csv')
# 各个特征缺失值汇总情况
data.isnull().sum()

"""
BuildingArea 这一特征缺失值最多，尝试对这一特征值进行预测

Suburb               0
Address              0
Rooms                0
Type                 0
Price             7610
Method               0
SellerG              0
Date                 0
Distance             1
Postcode             1
Bedroom2          8217
Bathroom          8226
Car               8728
Landsize         11810
BuildingArea     21115
YearBuilt        19306
CouncilArea          3
Lattitude         7976
Longtitude        7976
Regionname           3
Propertycount        3
dtype: int64

"""
# 选择连续性的特征来对 BuildingArea 进行预测
data_selected = data[['Rooms','Bedroom2', 'Bathroom', 'Car', 'Landsize', 'Lattitude', 'Longtitude', 'Propertycount', 'BuildingArea']]
import seaborn as sns
import matplotlib.pyplot as plt

# 查看各个特征之间的相关性
sns.heatmap(data_selected.corr(),linewidths=0.1,vmax=1.0, 
            square=True, linecolor='white', annot=True)
fig=plt.gcf()
fig.set_size_inches(20,12)
plt.show()
```

![`uploadfiles.nowcoder.com/files/20210201/897353_1612167335013/0081Kckwly1gklgchwfldj30nz0kr3zp.jpg`](img/e45ea23209319f9eef15953ac00b3476.png)

```cpp
"""
从上面的图可以看出，Rooms, Bedroom2, Bathroom, Car, Landsize 与 BuildingArea
有很强的相关性，因此选择这几个特征来预测 BuildingArea
"""
# 训练集不含有缺失值
data_selected =  data_selected[['Rooms','Bedroom2', 'Bathroom', 'Car', 'Landsize','BuildingArea']]

train_data = data_selected.dropna()
X_train = train_data.drop(columns= ['BuildingArea'])

y_train = train_data['BuildingArea']
y_train = y_train.values.astype('int')
# 测试集为含有缺失值的特征
X_test = data_selected[data_selected['BuildingArea'].isnull()]
X_test = X_test.drop(columns=['BuildingArea']).dropna()
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis

model.fit(X_train,y_train)
y_pred  = model.predict(X)# 这样就得到了缺失值的预测值，然后用它们来回填
```

**扩展**：使用 scikit-learn 工具包的 Imputer 类和 Pipeline 类进行缺失值的替换。

*   scikit-learn 是一个 Python 第三方提供的非常强大的机器学习库，它包含了从数据预处理到训练模型的各个方面的实现。在实际工作中，使用 scikit-learn 中可以减少我们的代码量，从而极大的减少我们写代码的时间，使我们有更多的精力去分析数据、调整参数及提升模型表现。

*   Imputer 是用来填充缺失值的主要工具类。与使用 pandas 手动计算平均值、中位数及众数然后在用它们填充缺失值的不同点在于，我们只需要告诉 Imputer 要用哪个统计值来填充就好了，Imputer 会根据我们指定的统计值进行填充

*   Pipeline 可以理解为工厂里的流水线。Pipeline 允许我们通过一系列步骤将数据从一种表示方式转换到另一种表示方式。例如，我们可以填充缺失的值，将输出传递给交叉验证和网格搜索，然后通过一系列步骤链式地拟合模型，其中一个步骤的输出是另一个步骤的输入。Pipeline 可以将许多算法模型串联起来，比如将特征提取、归一化、分类组织在一起形成一个典型的机器学习问题工作流。主要带来两点好处：第一个是直接调用 fit 和 predict 方法来对 pipeline 中的所有算法模型进行训练和预测；第二个是可以结合 grid search 对参数进行选择。

    代码如下：

```cpp
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import Imputer

# 指定要被填充的缺失值是‘Nan’，使用平均值进行填充
imp = Imputer(missing_values='NaN', strategy='mean') 
# 离散值可以使用众数进行填充
# imp = Imputer(missing_values='NaN', strategy=‘most_frequent') 

logreg = LogisticRegression()

steps = [('imputation', imp),('logistic_regression', logreg)]

# 使用 pipeline 将工作流程组装起来，包括缺失值填充和逻辑回归这两步
# 我们也可以添加机器学习的其他流程，如特征缩放 StandardScaler()
pipeline = Pipeline(steps)
X_train, X_test, y_train, y_test = train_test_split(X, y,
                                 test_size=0.3, random_state=42)
pipeline.fit(X_train, y_train)
y_pred = pipeline.predict(X_test)
pipeline.score(X_test, y_test)
```

* * *

# 七、经典机器学习模型对缺失值的敏感度

| 机器学习模型 | 是否敏感 | 原因 |
| :-: | :-: | :-- |
| 线性回归 | 是 | 线性模型的参数拟合可能依赖于代价函数收敛而获得，而代价函数(cost function)往往涉及到距离(distance)的计算，即计算预测值和真实值之间的差值，这容易导致对缺失值敏感 |
| 逻辑回归 | 是 | 逻辑回归本质上也是线性模型，所以原因与线性回归类似 |
| SVM 支持向量机 | 是 | SVM 是一种二分类模型，它的基本模型是在特征空间中寻找间隔最大化的分离超平面的线性分类器，间隔最大化是它的独特之处，通过该超平面实现对未知样本集的分类。超平面由几个关键点(支持向量)来确定，这些关键点支撑起了一个超平面，而缺失值很可能是其中的一个“点”，所以缺失值会影响影响超平面的划分，从而导致很难正确的分类；并且 SVM 没有处理缺失值的策略。而 SVM 希望样本在特征空间中线性可分，所以特征空间的好坏对 SVM 的性能很重要。缺失特征数据将影响训练结果的好坏 |
| KNN | 是 | KNN 即最邻近算法，其过程可以总结为计算训练样本和测试样本中每个样本点的距离(常见的距离度量有欧式距离，马氏距离等)；对上面所有的距离值进行排序(升序);选前 k 个最小距离的样本；根据这 k 个样本的标签进行投票，得到最后的分类类别；根据 KNN 的过程，可以知道其对缺失值敏感的原因与 SVM 支持向量机类似，同样涉及到了距离度量(distance measurement)，比如计算两个点之间的距离，缺失数据就变得比较重要。因为涉及到“距离”这个概念，那么缺失值处理不当就会导致效果很差 |
| 朴素贝叶斯 | 否 | 朴素贝叶斯算法能够处理缺失的数据，在算法的建模时和预测时数据的属性都是单独处理的。因此如果一个数据实例缺失了一个属性的数值，在建模时将被忽略，不影响类条件概率的计算，在预测时，计算数据实例是否属于某类的概率时也将忽略缺失属性，不影响最终结果 |
| 决策树 | 否 | 决策树有自己的机制来处理缺失值。以 C4.5 算法为例，缺失值的问题处理可以从三个阶段来考虑：第一：当开始决定选择哪个属性用来进行分支时；第二：如果有些训练样本缺失了某些属性值时该如何处理；第三：一个属性已被选择，那么在决定分支的时候如果有些样本缺失了该属性该如何处理当决策树已经生成，但待分类的样本缺失了某些属性，这些属性该如何处理。具体过程参考周志华的《机器学习》一书。 |
| 随机森林 | 否 | 随机森林同样对缺失值有一套处理流程。首先给缺失值假设一些预估值，比如使用一些统计值，如选择非缺失值的中位数或众数作为当前缺失值的估计值；然后使用填补后的训练集来训练随机森林模型，并记录每一组数据在决策树中的一步步路径，判断哪组数据和缺失数据路径最相似，引入相似矩阵(相似度矩阵就是任意两个观测实例间的相似度矩阵，原理是如果两个观测实例落在同一棵树的相同节点次数越多，则这两个观测实例的相似度越高)，来记录数据之间的相似度，比如有 N 组数据，相似度矩阵大小就是 N*N；如果缺失值是类别变量，通过权重投票得到新估计值，如果是数值型变量，通过加权平均得到新的估计值，如此迭代，直到得到稳定的估计值 |

* * *

# 八、相关面试题

**1\. 如何使用 pandas 统计给定列里各个值出现的次数？**
提示：value_counts 函数

**2\. 如何获得 panda DataFrame 中一个列的平均值、中位数、众数？**
提示：mean 函数、median 函数、mode 函数

**3\. Pandas 中使用的标准数据缺失标志是什么？**
NaN

**4\. 检查数据中是否含有任何缺失值**
提示：data.isnull().values.any()；除此之外，是否还有其他方法？

**5\. 查看每列数据缺失值情况**
提示：data.isnull().sum()；除此之外，是否还有其他方法？

**6\. 删除所有存在缺失值的行**
提示：dropna 函数

**7\. 按行计算 df 的每一行均值**
提示：mean 函数

**8\. 如何将非标准的标记如"missing", "not available", "NA"识别为缺失值？**
提示：read_csv()方法

**9\. 怎么理解决策树能处理缺失值，而模型 SVM 对缺失值比较敏感？**
提示：决策树有缺失值处理机制；不同算法在不同阶段如何去做的；SVM 的超平面划分是由什么决定的？

**10\. 缺失值常用处理方法？各自的利弊？**
提示：总体上有两种，即删除和填充。它们各自适用场景，以及如何操作

**11\. 随机森林如何处理缺失值？**
提示：随机森林有它自己处理流程

**12\. 你会如何进行探索性数据分析(EDA)？**
数据集概览；数据可视化；缺失值；异常值；重复值；类型转换；是否构建新特征

**13\. ID3、c4.5、cart、随机森林到底是如何处理缺失值的？**
提示：参考第七部分：经典机器学习模型对缺失值的敏感度

**14\. 连续型变量和离散型变量分别如何处理缺失值？**
提示：连续型：平均数或中位数数；离散型：考虑众数