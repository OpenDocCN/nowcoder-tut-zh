# 第三章 第 9 节 网易 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/bda2c9049dc74299bb96bf1e571f148a`](https://www.nowcoder.com/tutorial/10028/bda2c9049dc74299bb96bf1e571f148a)

# 网易 2020 秋招笔试真题

![](img/8adbddf8c0bcff34ec7af35428e544aa.png)

### 1、最小数位和

【题目描述】定义 S(n)，表示 n 在十进制下的各位数字和。

现在给定一个 x,请你求出最小正整数 n，满足 x≤S(n).

**输入描述**

第一行数据组数 T，对于每组数据，一行一个数字*x*。

1≤x≤10⁵,1≤T≤10

**输出描述**

对于每组数据，一行一个整数表示最小的 n。

**示例 1**

**输入**

```cpp
2
7
9 
```

**输出**

```cpp
7
9
```

**示例****2**

**输入**

```cpp
2 
13
18
```

**输出**

```cpp
49  
99
```

**【解题思路】**

构造题。10 进制最大数是 9，所以求 9 的除数就有几个 9，余数就是最大位的数。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int t;
void solve() {
    int x;
    scanf("%d", &x);
    if (x % 9)
        printf("%d", x % 9);
    x -= x % 9;
    for (int i = 1; i <= x / 9; i++)
        printf("9");
    printf("\n");
}
int main() {
    scanf("%d", &t);
    while (t--)
        solve();
    return 0;
}
```

### 2、吃葡萄

【题目描述】有三种葡萄，每种分别有 a,b,c 颗。有三个人，第一个人只吃第 1,2 种葡萄，第二个人只吃第 2,3 种葡萄，第三个人只吃第 1,3 种葡萄。
适当安排三个人使得吃完所有的葡萄,并且且三个人中吃的最多的那个人吃得尽量少。

**输入描述**

第一行数字 T，表示数据组数。

接下来 T 行，每行三个数 a,b,c

1≤a,b,c≤10¹⁸,1≤T≤10

**输出描述**

对于每组数据，输出一行一个数字表示三个人中吃的最多的那个人吃的数量。

**示例 1**

**输入**

```cpp
2
1 2 3
1 2 6
```

**输出**

```cpp
2
3
```

**示例 2**

**输入**

```cpp
1
12 13 11
```

**输出**

```cpp
12
```

**【解题思路】**

可以看成是三个人分别站在三角形的顶点（假定可以形成三角形）。设三角形两个短边是 a,b，长边是 c。则，若两短边之和大于等于长边的一半，可实现总数平分；反之，则结果为长边的一半。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
ll up(ll a, ll b) {
    if (a % b)
        return a / b + 1;
    return a / b;
}
int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        ll a, b, c;
        cin >> a >> b >> c;
        ll mx = max(max(a, b), c);
        ll ans = max(up(mx, 2), up(a + b + c, 3));
        cout << ans << endl;
    }
    return 0;
}
```

### 3、圆环切割

【题目描述】小易有 n 个数字排成一个环，你能否将它们分成连续的两个部分(即在环上必须连续)，使得两部分的和相等？

**输入描述**

第一行数据组数 T，对于每组数据
第一行数字 n，表示数字个数

接下来一行 n 个数，按顺序给出环上的数字。

2≤n≤100000,1≤A[i]≤109

**输出描述**

对于每组数据，一行输出 YES/NO

**示例 1**

**输入**

```cpp
1 
6
1 2 3 4 5 6
```

**输出**

```cpp
NO
```

**示例 2**

**输入**

```cpp
1
4
4 4 5 3
```

**输出**

```cpp
YES
```

**【解题思路】**

维护出前缀和数组，然后通过 set 容器做一个 hash 查找看是否能完成切割。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
#define ll long long
ll sum[100005];
set<ll> st;
void solve() {
    scanf("%d", &n);
    memset(sum, 0, sizeof(sum));
    st.clear();
    for (int i = 1; i <= n; i++) {
        int x;
        scanf("%d", &x);
        sum[i] = sum[i - 1] + x;
    }
    if (sum[n] & 1) {
        puts("NO");
        return;
    }
    for (int i = 1; i <= n; i++) {
        ll s = sum[i] - sum[n] / 2;
        if (st.find(s) != st.end()) {
            puts("YES");
            return;
        }
        st.insert(sum[i]);
    }
    puts("NO");
    return;
}
int main() {
    int t;
    scanf("%d", &t);
    while (t--)
        solve();
    return 0;
}
```

 ### 4、跳柱子

【题目描述】小易有 n 根柱子，第 i 根柱子的高度为 h[i]。一开始小易站在第一根柱子上。小易能从第 i 根柱子跳到第 j 根柱子，当且仅当 h[j]≤h[i]且 1≤j−i≤k。其中 k 为指定的一个数字。
另外小易拥有一次释放超能力的机会。这个超能力能让小易从柱子 i 跳到任意满足 1≤j−i≤k 的柱子 j 而无视柱子高度的限制。
现在小易想知道，小易是否能到达第 n 根柱子。

**输入描述**

第一行数据组数 T

对于每组数据，第一行数字 n,k 接下来一行 n 个数字表示 h[i]。

1≤n≤1000,1≤h[i]≤109,1≤T≤10,1≤k≤n

**输出描述**

对于每组数据，输出 YES 或 NO

**示例 1**

**输入**

```cpp
1
5 3
6 2 4 3 8
```

**输出**

```cpp
YES
```

**示例 2**

**输入**

```cpp
1
5 2
1 8 2 3 4
```

**输出**

```cpp
NO
```

**【解题思路】**

考虑 dp。令 dp[i][0]和 dp[i][1]表示在不使用/使用超能力的情况下是否能到达柱子。每次枚举前面的 k 个柱子并更新 dp[i][0]和 dp[i][1]即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int h[1005];
int f[1005][2];
int n, k;
void solve() {
    scanf("%d%d", &n, &k);
    memset(f, 0, sizeof(f));
    for (int i = 1; i <= n; i++)
        scanf("%d", &h[i]);
    f[1][0] = f[1][1] = 1;
    for (int i = 2; i <= n; i++) {
        for (int j = i - 1; j >= 1 && j >= i - k; j--) {
            if (f[j][0]) {
                f[i][1] = 1;
                if (h[j] >= h[i])
                    f[i][0] = 1;
            }
            if (f[j][1]) {
                if (h[j] >= h[i])
                    f[i][1] = 1;
            }
        }
    }
    if (f[n][1])
        puts("YES");
    else
        puts("NO");
    return;
}
int main() {
    int t;
    scanf("%d", &t);
    while (t--)
        solve();
    return 0;
}
```

### 5、乘积

【题目描述】小易给定你一个长度为 n 的正整数序列*A**[i]*，你每次可以使用 1 的代价将某个数加一或者减一，你希望用最少的代价使得所有数的乘积等于 B，求最小代价（操作结束后每个数也必须是正整数）。

**输入描述**

第一行数字 n, B,表示序列长度和目标乘积。

接下来一行 n 个正整数表示初始序列。

1≤n≤10³,1≤B≤10⁵，1≤A[i]≤10⁵.

**输出描述**

一行一个数字表示答案

**示例 1**

**输入**

```cpp
5 12
1 3 9 2 6
```

**输出**

```cpp
10
```

**说明**

把 3 变为 1 需要 2 的代价，把 9 变为 1 需要 8 的代价，总代价为 10。

**示例 2**

**输入**

```cpp
3 15
3 8 7
```

**输出**

```cpp
9
```

**说明**

把 8 变为 5 需要 3 的代价，把 7 变为 1 需要 6 的代价，总代价为 9。

**【解题思路】**

直接令 f[i][j]，表示让前 i 个数字乘积为 j 的最小代价，直接转移复杂度是 O(nA(B)²)，其中 A(B)表示 B 的约数个数，通常情况下不会很大，可以通过数据。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int f[1005][105];
int n, b;
int a[1005];
vector<int> v[100005];
int num[100005];
int main() {
    scanf("%d%d", &n, &b);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    for (int i = 1; i <= b; i++) {
        for (int j = i; j <= b; j += i)
            v[j].push_back(i);
    }
    for (int i = 0; i < v[b].size(); i++)
        num[v[b][i]] = i;
    f[0][0] = 0;
    for (int i = 1; i < v[b].size(); i++)
        f[0][i] = 1e9;
    for (int i = 1; i <= n; i++) {
        for (int j = 0; j < v[b].size(); j++) {
            f[i][j] = 1e9;
            for (int k = 0; k < v[v[b][j]].size(); k++) {
                f[i][j] = min(f[i][j], f[i - 1][num[v[v[b][j]][k]]] +
                                           abs(a[i] - v[b][j] / v[v[b][j]][k]));
            }
        }
    }
    printf("%d\n", f[n][v[b].size() - 1]);
    return 0;
}

```