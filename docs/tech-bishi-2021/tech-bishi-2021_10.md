# 第三章 第 3 节 百度 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/5f044cee77bf4f5f9becbec7c569c4fe`](https://www.nowcoder.com/tutorial/10028/5f044cee77bf4f5f9becbec7c569c4fe)

# 百度 2020 秋招笔试真题

**![](img/7671b1066ac15c0a1cde5c9d82e214d7.png)** 

### 1、度度熊的工作

【题目描述】老板给度度熊分配了 n 个工作，第 i 个工作需要耗费*a**[i]*单位时间，每个工作必须老板给定的限制时间*b**[i]*前完成。
度度熊从 0 时刻开始工作，在同一时间度度熊手上只能做一件工作，度度熊想知道他是否能把所有工作都完成呢？

**输入描述**

第一行一个数 T 表示数据组数。 (1≤T≤10)
每组数据第一行一个数 n。 (1≤n≤2×105)
接下来 n 行每行两个数表示 a[i]，b[i]。 (1≤a[i],b[i]≤109)

**输出描述**

每组数据输出一行，如果度度熊能完成他的工作输出"Yes"不然输出“No”。

**示例 1**

**输入**

```cpp
1
5
2 4
1 9
1 8
4 9
3 12
```

**输出**

```cpp
Yes
```

**说明**

从前往后依次做每个工作即可完成。

**【解题思路】**

用堆贪心实现。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

const int Maxn = 2e5;
int N;
struct Node {
    int t1, t2;
    bool operator<(const Node &rhs) const { return t2 < rhs.t2; }
} A[2 * Maxn + 5];
priority_queue<int> q;
int main() {
    int T;
    scanf("%d", &T);
    while (T--) {
        scanf("%d", &N);
        for (int i = 1; i <= N; i++)
            scanf("%d %d", &A[i].t1, &A[i].t2);
        sort(A + 1, A + N + 1);
        int t = 0, tot = 0;
        if (!q.empty())
            q.pop();
        for (int i = 1; i <= N; i++) {
            if (t + A[i].t1 <= A[i].t2) {
                tot++;
                t += A[i].t1;
                q.push(A[i].t1);
            } else {
                if (q.empty())
                    continue;
                int maxx = q.top();
                if (A[i].t1 < maxx) {
                    q.pop();
                    q.push(A[i].t1);
                    t = t - maxx + A[i].t1;
                }
            }
        }
        if (tot == N)
            puts("Yes");
        else
            puts("No");
    }
    return 0;
}
```

### 2、小度部队

【题目描述】小度的特种部队一共有 n 名士兵, 一天小度派所有士兵去探索野区。士兵们出发时沿着一条道路行进, 直到遇到三岔路口。
小度在出发前就给部队部署了部队划分规则:当遇到三岔路口的时候, 部队若可以分为两个部分，并且两个部分的人数差恰好为 k,那么就完成部队划分,划分的两个部分分别沿着两条路行进下去，否则该部队的所有士兵就在此位置停下扎营。
野区内有不计其数的三岔路口, 所以整个部队的每一个部分最终都会停下扎营，小度想知道最终扎营的总数为多少？

**输入描述**

包括两个整数 n,k(1<=n<=10⁹, 1<= k<=1000), 即部队中士兵的总人数和划分部队的参数。

**输出描述**

一个整数，表示最终答案。

**示例 1**

**输入**

```cpp
10 2
```

**输出**

```cpp
5
```

**说明**

```cpp
10
/ \
6 4
/\ /\
4 2 3 1
/\
3 1
最终叶子节点个数即答案:5
```

**【解题思路】**

递归搜索所有情况即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
#define ll long long
using namespace std;

ll k, n;
ll dfs(int v) {
    if (v <= k)
        return 1;
    if ((v - k) % 2 == 0)
        return (dfs((v - k) / 2) + dfs((v + k) / 2));
    else
        return 1;
}
int main() {
    scanf("%lld%lld", &n, &k);
    printf("%lld", dfs(n));
    return 0;
}
```

### 3、返回公司

【题目描述】度度熊迷路了他想返回他的公司，他现在在 1 号点，他的公司在 n 号点。度度熊所在的城市由 n 个点和 m 条边组成，因为度度熊走了一天了很累，他还有走两条边的体力，度度熊想知道他能否回到公司呢？

**输入描述**

第一行一个数 T 表示数据组数 (1≤T≤10)
每组数据第一行两个数 n,m。含义见题意。 (3≤n≤2×105,1≤m≤105)
接下来 m 行每行两个数 a[i],b[i] 表示 a[i]到 b[i]之间有一条边

**输出描述**

每组数据一行如果能回到公司输出“POSSIBLE”，不能输出"IMPOSSIBLE"。

**示例 1**

**输入**

```cpp
1
4 3
1 2
2 3
3 4
```

**输出**

```cpp
IMPOSSIBLE
```

**【解题思路】**

图论问题，枚举经过一个中转点是否能到终点。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

const double g = 10.0, eps = 1e-7;
const int N = 200000 + 10, maxn = 500 + 100, inf = 0x3f3f3f;

vector<int> v[N];
int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    int T;
    cin >> T;
    while (T--) {
        int n, m;
        cin >> n >> m;
        for (int i = 0; i <= n; i++)
            v[i].clear();
        for (int i = 0; i < m; i++) {
            int a, b;
            cin >> a >> b;
            v[a].push_back(b);
            v[b].push_back(a);
        }
        for (int i = 0; i < v[1].size(); i++) {
            int u = v[1][i];
            for (int j = 0; j < v[u].size(); j++) {
                if (v[u][j] == n) {
                    cout << "POSSIBLE" << endl;
                    return 0;
                }
            }
        }
        cout << "IMPOSSIBLE" << endl;
    }
    return 0;
}
```

### 4、石子游戏

【题目描述】今天，度度熊和牛妹在玩取石子的游戏，开始的时候有 n 堆石头，第 i 堆有 a[i]个石头，两个人轮流动作，度度熊先走，在每个回合，玩家选择一个非空堆，并从堆中移除一块石头。如果一个玩家在轮到他之前所有的石堆都是空的，或者如果在移动石头之后，存在两个堆包含相同数量的石头（可能为都为 0），那么他就会输。假设两人都在游戏时选择最佳方式，度度熊和牛妹谁会赢？如果度度熊获胜，输出“man”，如果牛妹获胜，输出“woman”（输出不包含双引号）。

**输入描述**

第一行一个数表示 T 组数据 (1≤T≤10)
每组数据第一行一个数 n，表示 n 堆石头。 (1≤n≤105)
第二行 n 个数，表示每堆中石头的个数。 (0≤a[i]≤109)

**输出描述**

每组一行如果度度熊获胜，输出“man”，如果牛妹获胜，输出“woman”（输出不包含双引号）。

**示例 1**

**输入**

```cpp
2
1 
0
2
2 2
```

**输出**

```cpp
woman
man
```

**【解题思路】**

博弈问题。讨论 woman 的必胜状态，详情见代码。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int num[100005];
bool check(int n) {
    if (n >= 2 && num[0] == 0 && num[1] == 0)
        return 1;
    for (int i = 2; i < n; ++i)
        if (num[i] == num[i - 1] && num[i - 1] == num[i - 2])
            return 1;
    for (int i = 2; i < n; ++i)
        if (num[i] == num[i - 1] && num[i] == num[i - 2] + 1)
            return 1;
    int cnt = 0;
    for (int i = 1; i < n; ++i)
        if (num[i] == num[i - 1])
            ++cnt;
    return cnt > 1;
}
int main() {
    int T;
    cin >> T;
    while (T--) {
        int n;
        cin >> n;
        for (int i = 0; i < n; ++i)
            cin >> num[i];
        sort(num, num + n);
        if (check(n)) {
            cout << "woman" << endl;
            continue;
        }
        long long sum = 0;
        for (int i = 0; i < n; ++i)
            sum += num[i];
        if ((sum - n * (n - 1) / 2) % 2)
            cout << "man" << endl;
        else
            cout << "woman" << endl;
    }
    return 0;
}
```

### 5、还原数列

【题目描述】老板给度度熊 n 个数， 每一次从 a[i]中取出一个最大的减去 n，其他的 n-1 个数加上 1，一直重复直到最大的 a[i]<n，执行次数记为 k。
老板想知道最少执行多少次操作使得 n 个数都小于 n 呢？

**输入描述**

第一行一个数 (2≤n≤50)。
第二行 n 个数表示数列 ai。

**输出描述**

一个数表示 k

**示例 1**

**输入**

```cpp
3
1 0 3
```

**输出**

```cpp
1
```

**【解题思路】**

模拟计算答案即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
const int MaxN = 50;
LL a[MaxN + 5];
LL cnt[MaxN + 5];
int n;

int main() {
    while (~scanf("%d", &n)) {
        for (int i = 1; i <= n; i++)
            scanf("%lld", &a[i]);
        LL tot = 0, ans = 0;
        bool flag = true;
        while (flag) {
            flag = false;
            tot = 0;
            for (int i = 1; i <= n; i++) {
                cnt[i] = a[i] / n;
                a[i] = a[i] % n;
                tot += cnt[i];
            }
            ans += tot;
            for (int i = 1; i <= n; i++) {
                a[i] += tot - cnt[i];
                if (a[i] >= n)
                    flag = true;
            }
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```