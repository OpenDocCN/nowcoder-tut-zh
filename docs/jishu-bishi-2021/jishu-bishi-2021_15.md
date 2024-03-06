# 第三章 第 8 节 拼多多 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/0f9cf51539b84d4885a705560aeb4926`](https://www.nowcoder.com/tutorial/10028/0f9cf51539b84d4885a705560aeb4926)

#  拼多多 2020 秋招笔试真题

![](img/37bb1304056999573f52219944197a40.png)

### 1、多多的魔术盒子

【题目描述】有 N 个魔术盒子（编号 1～N），其中编号为 i 的盒子里有 i 个球

每次选择一个数字 X（1<=X<=N），把球数量大于等于 X 个的盒子里的球减少 X 个

求最少的次数将所有盒子里的球减少为空

**【解题思路】**

从题意中易分析得，目标的次数随着 N 的增长为单调不降函数

设 N 的最少操作次数为 K，N / 2 需要的次数为 K - 1；即可以通过选择 X = N / 2， 将原问题变成 N / 2 的子问题

而对于 N=1 的边界情况，需要 K=1 次操作，因此可以得到通项公式： Log(N) / Log(2) + 1

时间复杂度: O(1)

空间复杂度: O(1)

另外还可以采取其他递推，递归，动态规划的方法均可，可以得到同样的结果

考虑知识点：

递推，递归，动态规划，实现

**【参考代码】**

```cpp
#include <iostream>
#include <cmath>

using namespace std;

int main()
{
  int T, n;
  cin >> T;
  while (T--) {
    cin >> n;
    printf("%d\n", (int)(log(n) / log(2)) + 1);
  }
  return 0;
}
```

### 2、多多的排列函数

【题目描述】给出一个 N 的全排列数组{A[i]}，定义函数 F

F(1)=A[1]

F(i)=|F(i-1)-A[i]|,(i>1)

对于 100%的数据：1<=N<=100,000

求在所有可能的数列{A[n]} 中，F(N)的最小值和最大值分别是多少？

**【解题思路】**

数排列的数组：

将数字从大到小排列，两两相减取绝对值即可；另外还可以通过找规律分析，最小值只有 0 和 1 两种可能，周期为 4。

对于最大值，N 减去 F(N-1)的最小值即可。

时间复杂度: O(N)

空间复杂度: O(N)

**【参考代码】**

```cpp
#include <iostream>

using namespace std;

int a0[100010], a1[100010];
int b0[100010], b1[100010];

int main()
{
  int T;
  cin >> T;
  while (T--) {
    int N;
    cin >> N;

    int c0, c1;
    for (int i = 1; i <= N; i++) a0[i] = N - i + 1;
    for (int i = 1; i <  N; i++) a1[i] = N - i; a1[N] = N;

    b0[1] = a0[1];
    for (int i = 2; i <= N; i++) b0[i] = abs(b0[i - 1] - a0[i]);

    b1[1] = a1[1];
    for (int i = 2; i <= N; i++) b1[i] = abs(b1[i - 1] - a1[i]);

    cout << b0[N] << " " << b1[N] << endl;
  }

  return 0;
}
```

### 3、多多的电子字典

【题目描述】有一个只由单词 a 和 b 组成的词典。每个单词，a 的数量不超过 N，b 的数量不超过 M。求字典序中第 K 小的单词

· 对于 40%的数据：0<K<100,000

· 对于 100%的数据：0<N, M<50, 0<K<1,000,000,000,000,000

**【解题思路】**

对于 40%的数据，可以通过枚举 a 和 b 加上排序比较的方法可以暴力求出

时间复杂度：O(K)

空间复杂度：O(K)

而对于 100%的数据：

第一步，求 N 个 a 和 M 个 b 可以组成多少个不同的单词，可以使用动态规划计数 （也可以用其他公式计数）

F[i][j] 表示使用 i 个 a 和 j 个 b 时，可以组成的单词个数

转移方程： 

最后一位填了'a': F[i + 1][j] += F[i][j]

最后一位填了'b': F[i][j + 1] += F[i][j]

初始条件：

F[0][0] = 1

第二步，从高位开始枚举，当前最高为‘a’，那么剩余 N - 1 个 a 可选和 M 个 b 可选

可以有 sum = Σ(f[i][j]) （其中 0 <= i <= N - 1, 0 <= j <= M） 个单词

如果 K > sum, 说明当前位不可能为'a', 因为剩下的所有单词组合个数都不够 K，所以只能是'b'l，否则就为'a'

重复这个枚举的过程，直到找到 K

时间复杂度: O(N * M)

空间复杂度: O(N * M)

**【参考代码】**

```cpp
#include <iostream>

using namespace std;

long long f[51][51];

int main()
{
  long long N, M, K;
  cin >> N >> M >> K;
  f[0][0] = 1;
  for (int i = 0; i <= N; i++) {
    for (int j = 0; j <= M; j++) {
      f[i + 1][j] += f[i][j];
      f[i][j + 1] += f[i][j];
    }
  }

  string ans;
  while (true) {

    // if head is 'a':
    long long sum = 0;
    for (int i = 0; i <= N - 1; i++) {
      for (int j = 0; j <= M; j++) {
        if (sum > K) goto next;
        sum += f[i][j];
      }
    }

next:
    if (K > sum) {
      K -= sum;
      ans += 'b';
      M--;
    }
    else {
      ans += 'a';
      N--;
    }
    K--;

    if (K == 0) break;
  }

  cout << ans << endl;

  return 0;
}
```

### 4、骰子期望

【题目描述】有 N 个骰子，每个有数字 1～X[i]，求同时扔这 N 个骰子，最大值的期望 

对于 100%的数据： 1<=N<=50，1<=X[i]<=50

**【解题思路】**

由于最大值的存在，可以枚举当前最大值为 X，计算最大值小于等于为 X 的概率为：

P(X) = P({ A[i]<= X }) = ∏(A[i] / X[i]）（其中有：A[i]<= X[i]）

边界情况：如果 A[i] > Xi, 那么可忽略第 i 项

所以最大值为 X 的期望为：

E(X) = (P(X) - P(X - 1)) * X

边界情况：P(0) = 0

最后答案为:ans =∑(E(X))

时间复杂度: O(N * K)，其中 K 为最大元素值

空间复杂度: O(N)

【**参考代码】**

```cpp
#include <iostream>
#include <vector>

using namespace std;

int data[100];

int main()
{
  int N;
  cin >> N;
  int K = 0;
  for (int i = 1; i <= N; i++) {
    cin >> data[i];
    K = max(K, data[i]);
  }

  double pre = 0.0;
  double ans = 0.0;
  for (int i = 1; i <= K; i++) {
    double p = 1.0;
    for (int j = 1; j <= N; j++) {
      if (i > data[j]) {
        continue;
      }
      p *= 1.0 * i / data[j];
    }
    ans += (p - pre) * i;
    pre = p;
  }

  printf("%.2f\n", ans);

  return 0;
}
```

### 5、二维表第 k 大数

【题目描述】给出一个 N*M 的矩阵，其中(i,j)的元素为 i*j, 求这个矩阵中第 K 大的元素

对于 30%的数据：1<=n, m<=1000

对于 100%的数据：1<=n, m<=40000

**【解题思路】**

对于 30%的数据：

暴力枚举矩阵中的每个元素，然后排序求出第 K 个数字

时间复杂度：O(N * M * Log(N * M))

对于 100%的数据：

由于元素大小与元素的排名是单调正相关

所以使用二分法，枚举可能的答案，再求其排名

具体为如果当前元素为 X，那么再枚举 i（1～N），求有多少个 j，使得 i * j >= X

将所有的 j 求和即为元素 X 的排名

如果 X 的排名大于 K，说明 X 比答案的元素要小，再继续二分枚举右区间；反之则枚举左区间

缩小范围直到求得排名等于 K 的元素，即为答案元素

时间复杂度: O(N * Log(N * M))

空间复杂度: O(1)

**【参考代码】**

```cpp
#include <iostream>

using namespace std;

int main()
{
  long long N, M, K;
  cin >> N >> M >> K;
  if (N > M) swap(N, M);

  long long L = 1, R = N * M;
  long long ans = -1;
  while (L <= R) {
    long long mid = (L + R) >> 1;

    long long now = 0;
    for (int i = N; i >= 1; i--) {
      int j = (mid + i - 1) / i;
      if (j <= M) {
        now += M - j + 1;
      }
    }
    if (now >= K) {
      ans = mid;
      L = mid + 1;
    } else {
      R = mid - 1;
    }
  }

  cout << ans << endl;

  return 0;
}

```