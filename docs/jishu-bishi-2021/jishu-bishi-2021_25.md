# 第三章 第 18 节 网易互娱 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/11b12ecacbd64d7bb74303efe81c41af`](https://www.nowcoder.com/tutorial/10028/11b12ecacbd64d7bb74303efe81c41af)

# 网易互娱 2020 秋招笔试真题

![](img/d0996e66fda3eb4e8c4960d04f3faf6f.png)

### 1、二进制计数

【题目描述】小 A 刚学了二进制，他十分激动。为了确定他的确掌握了二进制，你给他出了这样一道题目：给定 N 个非负整数，将这 N 个数字按照二进制下 1 的个数分类，二进制下 1 的个数相同的数字属于同一类。求最后一共有几类数字？

**输入描述**

输入的第一行是一个正整数 T（0<T<=10），表示样例个数。对于每一个样例，第一行是一个正整数 N（0<N<=100），表示有多少个数字。接下来一行是 N 个由空格分隔的非负整数，大小不超过 2³¹–1。

**输出描述**

对于每一组样例，输出一个正整数，表示输入的数字一共有几类。

**示例 1**

输入

```cpp
1
5
8 3 5 7 2
```

输出

```cpp
3
```

**【解题思路】**

用二进制 1 的个数作为 hash 的 key 统计答案即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

int get_bits(int num) {
    if (num == 0)
        return 0;
    return num % 2 + get_bits(num / 2);
}

int main() {
    int T, n;
    scanf("%d", &T);
    while (T--) {
        unordered_map<int, int> mp;
        scanf("%d", &n);
        int num, bits;
        for (int i = 0; i < n; ++i) {
            scanf("%d", &num);
            bits = get_bits(num);
            if (mp.find(bits) == mp.end())
                mp[bits] = 1;
            else
                mp[bits]++;
        }
        printf("%d\n", mp.size());
    }
}
```

### 2、游泳池

【题目描述】小明作为一个游泳池管理员，以玩弄给水管和排水管为乐，也因此产生了很多数学题考验小朋友。

现在小明想把这个行动升级，考验一下程序员，做了一个自动装置来控制给水管和排水管。在开始时，给水管和排水管都是打开状态的，并且游泳池里没有水。在自动装置的作用下，每经过 t1 分钟，给水管的状态都会改变，即从打开状态变为关闭状态或从关闭状态变为打开状态，而同时每经过 t2 分钟，排水管的状态也会改变。当给水管打开时，给水管每分钟会向游泳池里注入 m1 升水；当排水管打开时，排水管每分钟会把游泳池里水排走 m2 升；当给水管和排水管同时打开时，游泳池的水量变化为每分钟(m1-m2)升。当然泳池的水量不能变为负数，同时泳池也有个最大容量 m，水量不能超过 m 升。那么经过 t 分钟后，游泳池里有多少升水？

**输入描述**

输入第一行为一个正整数 T，表示有 T 组数据。
每组数据的为一行包含六个整数，分别表示 m, t, m1, t1, m2, t2。
数据范围：
对于所有数据，满足 1<=T<=10, 1<=m<=100000, 1<=t<=86400, 1<=m1,m2<=100, 1<=t1,t2<=10。

**输出描述**

对于每一个数据，输出一行，包括一个整数，为在 t 分钟后游泳池中的水量。

**示例 1**

**输入**

```cpp
5
10 2 1 5 2 5
10 2 10 5 2 5
10 2 3 5 2 5
100 100 3 4 4 3
10000 1000 10 5 5 3
```

**输出**

```cpp
0
10
2
3
2495
```

**【解题思路】**

模拟实现即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

static inline void assert_range(int x, int l, int r) {
    assert(x >= l && x <= r);
}

static bool is_open(int t, int t1) {
    if (t % (2 * t1) < t1) {
        return true;
    } else {
        return false;
    }
}
void cal() {
    int m, t, x1, x2, t1, t2;
    scanf("%d %d %d %d %d %d", &m, &t, &x1, &t1, &x2, &t2);
    assert_range(m, 1, 100000);
    assert_range(t, 1, 86400);
    assert_range(x1, 1, 100);
    assert_range(x2, 1, 100);
    assert_range(t1, 1, 10);
    assert_range(t2, 1, 10);
    int x = 0;
    for (int i = 0; i < t; i++) {
        if (is_open(i, t1)) {
            x += x1;
        }
        if (is_open(i, t2)) {
            x -= x2;
        }
        if (x < 0) {
            x = 0;
        } else if (x > m) {
            x = m;
        }
    }
    printf("%d\n", x);
    return;
}
int main() {
    int t;
    scanf("%d", &t);
    assert_range(t, 1, 10);
    while (t > 0) {
        t--;
        cal();
    }
    return 0;
}
```

### 3、幸运 N 串

【题目描述】小 A 很喜欢字母 N，他认为连续的 N 串是他的幸运串。有一天小 A 看到了一个全部由大写字母组成的字符串，他被允许改变最多 2 个大写字母（也允许不改变或者只改变 1 个大写字母），使得字符串中所包含的最长的连续的 N 串的长度最长。你能帮助他吗？

**输入描述**

输入的第一行是一个正整数 T（0<T<=20），表示有 T 组测试数据。对于每一个测试数据包含一行大写字符串 S（0<|S|<= 50000，|S|表示字符串长度）。
数据范围：
20%的数据中，字符串长度不超过 100；
70%的数据中，字符串长度不超过 1000；
100%的数据中，字符串长度不超过 50000。

**输出描述**

对于每一组测试样例，输出一个整数，表示操作后包含的最长的连续 N 串的长度。

**示例 1**

**输入**

```cpp
3
NNTN
NNNNGGNNNN
NGNNNNGNNNNNNNNSNNNN
```

**输出**

```cpp
4
10
18
```

**【解题思路】**

双指针，滑动窗口，当窗口内非 N 小于 2 的时候，可以一直更新最大 N 串的长度，当非 N 串大于 2 时，滑动窗口左指针，直到非 N 串数目小于 2，然后更新最大长度。

**【参考代码】**

```cpp
import sys
def count(line):
    # 左边界
    l = 0
    count_n = 0  # 用来统计当前窗口内 N 的个数
    res = 0
    for r, char in enumerate(line):

        if char == 'N':
            count_n += 1
        # 最多 2 个改变:r-l+1 表示窗口大小，在窗口大小中如果非 N 的数目大于 2，则滑动窗口左指针，直到窗口内的非 N 小于 2
        while r - l + 1 - count_n > 2:
            if line[l] == 'N':
                count_n -= 1
            l += 1
        res = max(res, r - l + 1)
    return res

if __name__ == "__main__":
    # 读取第一行的 n
    n = int(sys.stdin.readline().strip())
    ans = []
    for i in range(n):
        # 读取每一行
        v = str(sys.stdin.readline().strip())
        ans.append(count(v))
    for i in ans:
        print(i)
```

### 4、水平线

【题目描述】伞屉国是一个以太阳能为主要发电手段的国家，因此他们国家中有着非常多的太阳能基站，链接着的基站会组合成一个发电集群。但是不幸的是伞屉国不时会遭遇滔天的洪水，当洪水淹没基站时，基站只能停止发电，同时被迫断开与相邻基站的链接。你作为伞屉国的洪水观察员，有着这样的任务：在洪水到来时，计算出发电集群被洪水淹没后被拆分成了多少个集群。

由于远古的宇宙战争的原因，伞屉文明是一个二维世界里的文明，所以你可以这样理解发电基站的位置与他们的链接关系：给你一个一维数组 a，长度为 n，表示了 n 个基站的位置高度信息。数组的第 i 个元素 a[i]表示第 i 个基站的海拔高度是 a[i],而下标相邻的基站才相邻并且建立链接，即 x 号基站与 x-1 号基站、x+1 号基站相邻。特别的，1 号基站仅与 2 号相邻，而 n 号基站仅与 n-1 号基站相邻。当一场海拔高度为 y 的洪水到来时，海拔高度小于等于 y 的基站都会被认为需要停止发电，同时断开与相邻基站的链接。

**输入描述**

每个输入数据包含一个测试点。
第一行为一个正整数 n，表示发电基站的个数 (0<n<=200000)。
接下来一行有 n 个空格隔开的数字，表示 n 个基站的海拔高度，第 i 个数字 a[i]即为第 i 个基站的海拔高度，对于任意的 i(1<=i<=n),有(0<=a[i]<2³¹-1)。
接下来一行有一个正整数 q(0<q<=200000)，表示接下来有 q 场洪水。
接下来一行有 q 个整数，第 j 个整数 y[j]表示第 j 场洪水的海拔为 y[j],对于任意的 j(1<=j<=n),有(-2³¹<y[j]<2³¹-1)。

**输出描述**

输出 q 行，每行一个整数，第 j 行的整数 ans 表示在第 j 场洪水中，发电基站会被分割成 ans 个集群。标准答案保证最后一个整数后也有换行。

**示例 1**

**输入**

```cpp
10
6 12 20 14 15 15 7 19 18 13 
6
15 23 19 1 17 24
```

**输出**

```cpp
2
0
1
1
2
0
```

**【解题思路】**

每次洪水来的时候会淹没掉一些发电机。如果我们能得到哪些发电机沉下去了，就可以枚举每一个发电机的编号来进行处理。

 而每个沉下去的发电机有三种情况。

1\. 两侧的发电机都沉下去了，集群数 - 1;

2\. 只有一侧的发电机沉下去，集群数不变;

3\. 两侧的发电机都未沉下去，集群数 + 1\.

但是如果在线做的话，显然会 TLE。如果洪水是逐渐上涨，那么每个发电机只会被淹没掉一次，沉下去的发电机不可能再浮起来。则我们对洪水高度和发电机都排序，每次不同的洪水高度，看成是洪水上涨又新淹没了一堆发电机，将其记录在答案数组中，最后一并输出即可。离线算法的典型运用。注意边界条件。我们可以假设 0 号和 n+1 号发电机一直在水下即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;

const double EPS = 1e-6;

#define MAXN 211111
#define ABS(x) ((x) < (-(EPS)) ? -(x) : ((x) > (EPS) ? (x) : 0))

int n, q;
int a[MAXN], y[MAXN], h[MAXN], ans[MAXN];
int t[MAXN], f[MAXN];
bool vis[MAXN];

unordered_map<int, vector<int>> h2idx;

int get_f(int x) {
    if (x == f[x])
        return x;
    return x = get_f(f[x]);
}

void init() {
    h2idx.clear();
    memset(t, 0, sizeof(t));
    memset(vis, 0, sizeof(vis));
    scanf("%d", &n);
    for (int i = 0; i < n; ++i) {
        scanf("%d", a + i);
        h[i] = a[i];
        h2idx[h[i]].push_back(i);
        f[i] = i;
    }
    scanf("%d", &q);
    for (int i = 0; i < q; ++i)
        scanf("%d", y + i);
}

void solve() {
    sort(h, h + n);
    int group = 0;
    for (int i = n - 1, k; i >= 0; i = k - 1) {
        t[i] = group;
        for (k = i; k - 1 >= 0 && h[k - 1] == h[k]; --k)
            t[k] = group;
        group += h2idx[h[i]].size();
        for (int j = 0; j < h2idx[h[i]].size(); ++j) {
            int pos = h2idx[h[i]][j];
            int flag = 0;
            if (pos > 0 && a[pos - 1] >= h[i]) {
                int aa = get_f(f[pos]);
                int bb = get_f(f[pos - 1]);
                if (aa != bb) {
                    ++flag;
                    f[bb] = aa;
                }
            }
            if (pos < n - 1 && a[pos + 1] >= h[i]) {
                int aa = get_f(f[pos]);
                int bb = get_f(f[pos + 1]);
                if (aa != bb) {
                    ++flag;
                    f[bb] = aa;
                }
            }
            group -= flag;
        }
    }
    for (int i = 0; i < q; ++i) {
        int idx = upper_bound(h, h + n, y[i]) - h - 1;
        if (idx < 0)
            ans[i] = 1;
        else
            ans[i] = t[idx];
    }
}

void print() {
    for (int i = 0; i < q; ++i)
        printf("%d\n", ans[i]);
}

int main() {
    init();
    solve();
    print();
    return 0;
}

```