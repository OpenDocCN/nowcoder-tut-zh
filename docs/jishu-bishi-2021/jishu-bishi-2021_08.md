# 第三章 第 1 节 腾讯 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/7af6a55df1b74158bdaff583caa200e9`](https://www.nowcoder.com/tutorial/10028/7af6a55df1b74158bdaff583caa200e9)

#  腾讯 2020 秋招笔试真题

![](img/8472b376d3eb25fe1ea760e824c10295.png)

### 1、电话号码

【题目描述】企鹅王国是一个奇特的国家，他们的电话号码包含 11 位数字，并且第一个数字一定是 8。比如 88888888888 就是企鹅王国的电话号码，但是 13888888888 和 000 就不是。
现在给出一个长度为 n 的字符串 s，这个字符串只包含数字。
每一次操作，你可以从这个字符串中删除任意一个字符。

对于给定的字符串，需要你判断是否能通过 0 次或若干次操作，把这个字符串变成企鹅王国的电话号码。

**输入描述**

第一行一个整数 t，表示测试用例的组数；
接下来 2∗t 行，每个测试用例包含 2 行；
第一行一个整数 n，表示字符串的长度；
第二行一个字符串 s。
输入满足 1<=t<=100,1<=n<=100，每个字符串只包含数字。

**输出描述**

能则输出"YES"，否则输出"NO"。

**示例 1**

**输入**

```cpp
2
11
88888888888
3
000
```

**输出**

```cpp
YES
NO
```

**【解题思路】**

按题意模拟实现即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        int n;
        scanf("%d", &n);
        string s;
        cin >> s;
        if (s[0] == '8') {
            int sz = s.size();
            if (sz >= 11)
                puts("YES");
            else
                puts("NO");
        } else {
            int ma = -1;
            int sz = s.size();
            for (int i = 0; i < sz; i++) {
                if (s[i] == '8') {
                    ma = i;
                    break;
                }
            }
            if (ma == -1 || sz - ma < 11)
                puts("NO");
            else
                puts("YES");
        }
    }
    return 0;
}
```

### 2、两两配对

【题目描述】小 Q 有 M(M 为偶数)名员工, 第 i 名员工完成工作的时候有一个拖延时间值 t[i]。
现在小 Q 手里有 M/2 份工作需要完成, 每一份工作都需要安排两名员工参与, 对于第 i 份工作所需完成的时间为两名员工的拖延时间值总和。

现在 M/2 份工作同时开始进行,小 Q 希望所有工作结束的时间尽量早, 请你帮小 Q 设计一个优秀的员工分配方案,使得用尽量少的时间完成所有工作,并输出工作所需的最短时间。

**输入描述**

第一行为一个正整数*n*(1<=*n*<=10⁵)。
接下来有 n 行，每行两个正整数 x 和 y，表示有 x 名员工的拖延时间值为 y(1<=y<=10⁹)。保证所有 x 的总和等于 M(2<=M<=10⁹), 保证 M 为偶数。

**输出描述**

输出工作所需的最短时间。

**示例 1**

**输入**

```cpp
3
1 8
2 5
1 2
```

**输出**

```cpp
10
```

**说明**

拖延值为 8 的和拖延值为 2 的组队，两名拖延值为 5 的组队，所以完成工作的时间为 10，这是时间最短的方案。

**【解题思路】**

贪心。最大的和最小的组合，次大的和次小的组合。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
struct node {
    int cnt, t;
} s[100001];
bool comp(node x, node y) { return x.t < y.t; }
int main() {
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n; ++i)
        scanf("%d%d", &s[i].cnt, &s[i].t);
    sort(s + 1, s + n + 1, comp);
    int minimum_t = 0;
    for (int left = 1, right = n; left <= right;) {
        int number = min(s[left].cnt, s[right].cnt);
        number /= (left == right) ? 2 : 1;
        minimum_t = max(minimum_t, s[left].t + s[right].t);
        s[left].cnt -= number;
        s[right].cnt -= number;
        left += (s[left].cnt == 0) ? 1 : 0;
        right += (s[right].cnt == 0) ? -1 : 0;
    }
    printf("%d", minimum_t);
}
```

### 3、分组

【题目描述】2020 年一共有 n 名员工参加入职培训，第 i 名员工拥有一个战力值 x[i]。
在入职培训中有一个小活动需要把所有员工分为 A、B 两组进行比拼对决。
为了让活动更有趣味性，小 Q 希望两组的人数尽量均分(即两组人数之差不超过 1)，并且要让两组尽量势均力敌(A 组的战力值之和与 B 组的战力值之和的差值尽量小)。
小 Q 把这个问题交给你了，希望你帮他完成这个分组问题。

**输入描述**

输入的第一行包含一个正整数 T(1<=T<=10),表示测试用例数。
对于每一组测试用例，输入的一行包括一个正整数 n(2<=n<=100),表示员工的数量。

接下来的一行,包含 n 个正整数 xi {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; ++i)
        scanf("%d", &x), a[x]++;
    memset(f, -1, sizeof(f));
    f[0][0][0] = 0;
    for (int i = 0; i < m + 2; ++i) {
        for (int t1 = 0; t1 <= 2; ++t1) {
            for (int t2 = 0; t2 <= 2; ++t2) {
                if (~f[i][t1][t2]) {
                    for (int t3 = 0; t3 <= 2; ++t3) {
                        if (t1 + t2 + t3 > a[i + 1])
                            continue;
                        f[i + 1][t2][t3] = max(
                            f[i + 1][t2][t3],
                            f[i][t1][t2] + t3 + (a[i + 1] - t1 - t2 - t3) / 3);
                    }
                }
            }
        }
    }
    printf("%d\n", f[m + 2][0][0]);
    return 0;
}
```

### 4、最小非零元素

【题目描述】Q 哥给了小 Q 一个长度为 n 正整数序列 a[i]。

Q 哥要求小 Q 重复以下操作步骤 k 轮:

1、发现最小的非零元素 x。

2、打印 x。

3、将序列中所有非零元素减 x。

小 Q 把这个艰巨的任务交给了你,希望你能帮帮他。 

**输入描述**

输入包括两行。

第一行包括两个正整数 n 和 k(1<=n,k<=10⁵)。

第二行包括 n 个正整数 ai。 

**输出描述**

输出 k 行,即每轮中的最小非零整数(如果到某轮所有元素都是 0,打印 0 即可)。

**示例 1**

**输入**

```cpp
7 5
5 8 10 3 6 10 8
```

**输出**

```cpp
3
2
1
2
2
```

**【解题思路】**

用一个小根堆维护元素模拟过程。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
priority_queue<int, vector<int>, greater<int>> Q;
int main() {
    int n, k;
    scanf("%d %d", &n, &k);
    for (int i = 1; i <= n; i++) {
        int x;
        scanf("%d", &x);
        Q.push(x);
    }
    int del = 0;
    while (k--) {
        while (!Q.empty() && Q.top() - del <= 0)
            Q.pop();
        if (Q.empty())
            printf("0\n");
        else {
            int x = Q.top();
            Q.pop();
            x -= del;
            printf("%d\n", x);
            del += x;
        }
    }
}
```

### 5、异或值

【题目描述】小 Q 有一个长度为 n 的非负整数序列 a[i],Q 哥同样有一个长度为 n 的非负整数序列 b[i]。每次小 Q 从自己序列中选取出一个数 a[i]，Q 哥会选取一个数 b[j]，进行相加得到 a[i]+b[j]。显然一共可以得到 n²个数,小 Q 和 Q 哥想知道这 n²个数的按位异或值为多少？

按位异或:遵循二进制相同取 0，相异取 1，例如

x=(5)[10]=(0101)[2]

y=(11)[10]=(1011)[2]

则 x 和 y 的按位异或结果为(1110)[2]=(14)[10] 

**输入描述**

```cpp
输入包括三行。
第一行包括一个正整数 n(1<=n<=200000)
第二行包括 n 个非负整数 ai(0<=ai<230)
第三行包括 n 个非负整数 bi(0<=bi<230)
```

**输出描述**

一行表示答案。

**示例 1**

**输入**

```cpp
5
1 2 1 0 0
1 2 3 0 0
```

**输出**

```cpp
2
```

**说明**

一共可以得到 25 个数:
2,3,4,1,1,
3,4,5,2,2,
2,3,4,1,1,
1,2,3,0,0,
1,2,3,0,0.
他们异或的结果为 2

**示例 2**

**输入**

```cpp
6
4 6 0 0 3 3
0 5 6 5 0 3
```

**输出**

```cpp
8
```

**【解题思路】**

然后对于第 i 位(即 2^i)，枚举数组 a（设枚举的下标为 j），如果 a[j]的第 i 位为 0（即(a[j]&(1<<i))==0）那么如果 a[j]和某个数 x 相加能使得这一位变成 1，会存在如下两种情况：

1.x 这位为 0，且 a[j]和 x 两个数低于这位的数(即%(1<<i)之后)相加大于等于(1<<i)，那么就说明这位一定可以从更低位进位变成 1；

2\. x 这位为 1，且 a[j]和 x 两个数低于这位的数(即%(1<<i)之后)相加小于(1<<i)，那么这位就不存在进位，然后 a[j]+x 的第 i 位就很自然的是 1（因为 x 的第 i 位是 1）。

 a[j]的第 i 位为 0 是同样的道理。所以这题的做法就是先记录下 b 数组中第 i 位为 0/1 的时候%(1<<i)的值，然后枚举位数 i，然后在枚举数组 a(枚举下标为 j)，然后可以计算出 res=(1<<i)-a[j]%(1<<i)，即让 a[j]在和 x 相加在第 i 位发生进位时，x%(1<<i)的最小值，然后就可以利用 lower_bound 算出数量，进而统计出对于每个数 a[j]，和 b 数组每个数字相加后能使得第 i 位是 1 的数量，然后求个总和，如果是奇数说明答案第 i 位为 1，偶数说明答案第 i 位为 0。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;

#define int long long

int pow2(int x) {
    if (x == 0)
        return 1;
    return 2 * pow2(x - 1);
}

signed main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n;
    cin >> n;
    vector<int> a(n), b(n);
    for (int i = 0; i < n; ++i)
        cin >> a[i];
    for (int i = 0; i < n; ++i)
        cin >> b[i];
    int ans = 0;
    for (int k = 0; k < 30; ++k) {
        vector<int> aa(n);
        vector<int> bb(n);
        for (int i = 0; i < n; ++i) {
            aa[i] = a[i] % pow2(k + 1);
            bb[i] = b[i] % pow2(k + 1);
        }
        sort(bb.begin(), bb.end());
        int res = 0;
        int t = pow2(k);
        for (int i = 0; i < n; ++i) {
            res += lower_bound(bb.begin(), bb.end(), 2 * t - aa[i]) -
                   lower_bound(bb.begin(), bb.end(), t - aa[i]);
            res += lower_bound(bb.begin(), bb.end(), 4 * t - aa[i]) -
                   lower_bound(bb.begin(), bb.end(), 3 * t - aa[i]);
        }
        if (res % 2 == 1)
            ans += pow2(k);
    }
    cout << ans << '\n';
    return 0;
}
```