# 第三章 第 16 节 360 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/a4f561ae5e614d1b9c295dff223297d7`](https://www.nowcoder.com/tutorial/10028/a4f561ae5e614d1b9c295dff223297d7)

# 360 2020 秋招笔试真题

![](img/c3917526ebe25246640d7137412a328d.png)

### 1、表面积

【题目描述】将长 N*M 厘米的矩形区域划分成 N 行 M 列（每行每列的宽度均为 1 厘米），在第 i 行第 j 列的位置上叠放 A[i,j]个边长为 1 厘米的正方体（1≤A[i,j]≤100），所有正方体就组成了一个立体图形，每个正方体六个面中的一部分会被其它正方体遮挡，未被遮挡的部分的总面积即为该立体图形的表面积，那么该立体图形的表面积是多少平方厘米？
**输入描述**
第一行包含两个整数 N 和 M，1≤N，M≤1000。
接下来 N 行，每行包含 M 个整数，第 i 行的第 j 个整数表示 A[i,j]。
**输出描述**
输出表面积的大小。
**输入样例**

```cpp
2 2
2 1
1 1
```

**输出样例**

```cpp
20
```

**【解题思路】**

几何题。统计处理遮挡和深度部分。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
#define N 1007
using namespace std;
int n, m, a[N][N];
int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= m; ++j)
            scanf("%d", &a[i][j]);
    int ans = n * m * 2;
    for (int i = 1; i <= n; ++i) {
        ans += a[i][1] + a[i][m];
            for (int j = 2; j <= m; ++j)
                if (a[i][j] > a[i][j - 1]) ans += a[i][j] - a[i][j - 1];
        for (int j = 1
            if (a[i][j] > a[i][j + 1])
                ans += a[i][j] - a[i][j + 1];
    }
    for (int i = 1; i <= m; ++i) {
        ans += a[1][i] + a[n][i];
        for (int j = 2; j <= n; ++j)
            if (a[j][i] > a[j - 1][i])
                ans += a[j][i] - a[j - 1][i];
        for (int j = 1; j < n; ++j)
            if (a[j][i] > a[j + 1][i])
                ans += a[j][i] - a[j + 1][i];
    }
    printf("%d", ans);
}
```

### 2、序列重组

【题目描述】在一个古老的国度，这个国家的人并不懂得进位，但是对取模情有独钟，因此诞生了一个经典的问题，给出两个在 m 进制下含有 n 位的数字，你可以分别将这两个数各位上的数字重新排列，然后将两个数按位对应相加并分别对 m 取模，这样显然可以得到一个新的 m 进制下的 n 位数(可能存在前导 0)，但是这个结果是不唯一的，问题来了，按照这样的操作，能够得到的最大的 m 进制下的数字是多少呢。
**输入描述**
输入第一行包含两个正整数 n,m 分别表示数字含有 n 位，和在 m 进制下。（n，m≤100000）
输入第二行和第三行分别包含 n 个整数，中间用空格隔开，每个整数都在 0 到 m-1 之间。每行第 i 个数表示的是当前数第 i 位上的数字。
**输出描述**
输出包含 n 个数字，中间用空格隔开，表示得到的最大的数字，从高位到低位输出，如 6 在 2 进制下输出 3 位的结果是 1 1 0。

**输入样例**

```cpp
5 5
4 4 1 1 1
4 3 0 1 2
```

**输出样例**

```cpp
4 4 3 3 2
```

**【解题思路】**

统计两个数每个数字出现的次数，从小到大对 a 的数位进行枚举，对应得贪心的使用 b 的数位去匹配相加，最终得到答案。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 1000005;
int a[maxn], b[maxn], c[maxn];
stack<int> st;
int main() {
    int n, m;
    cin >> n >> m;
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
        a[x % m]++;
    }
    for (int i = 0; i < n; i++) {
        int x;
        cin >> x;
        b[x % m]++;
    }
    for (int k = 0; k < 2; k++)
        for (int i = 0; i < m; i++) {
            while (a[i] > 0) {
                st.push(i);
                a[i]--;
            }
            while (st.size() && b[m - i - 1] > 0) {
                c[(m - i - 1 + st.top()) % m]++;
                b[m - i - 1]--;
                st.pop();
            }
        }
    for (int i = m - 1; i >= 0; i--) {
        while (c[i] > 0) {
            cout << i << " ";
            c[i]--;
        }
    }
    cout << endl;
}
```

### 3、寻找子串

【题目描述】英语老师看你老是在英语课上和周围同学交头接耳，所以给你布置了一份额外的家庭作业来惩罚惩罚你：你有一个字符串 s，请你在 s 的所有子串中，找到出现次数最多的子串，告诉老师它的出现次数。
**输入描述**
共一行，一个字符串 s，仅由英文小写字母组成，1≤|s|≤10000。
**输出描述**
一个正整数，表示最大出现次数。
**输入样例**

```cpp
aba
```

**输出样例**

```cpp
2
```

**【解题思路】**

最多的子串一定是单个字符，直接统计出现次数最多的字符即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;
char s[100010];
int cnt[300];

int main() {
    scanf("%s", s + 1);
    int n = strlen(s + 1);
    for (int i = 1; i <= n; ++i)
        cnt[s[i]]++;
    int ans = 0;
    for (int i = 0; i < 300; ++i)
        ans = max(ans, cnt[i]);
    printf("%d\n", ans);
    return 0;
}
```

### 4、散步

【题目描述】饭后散步是一个很好的习惯，一天晚上，小 A 在一条笔直的路上散步，起点在路上某处，但是因为路上没有标识，他并不知道这个位于路上的那个位置，现在将道路划分为 N-1 个等距的部分，你可以把这条路当成一个数轴，道路上的结点标记为 1~N，起点和终点只可能是这 N 个点中的一个。
但是小 A 还提供了一个重要信息，他每隔一段时间就会用手机看一下自己走了多远，记作 D，但是他并不记得他是朝着哪个方向走的，唯一可以确定的是，在两次看手机的间隔中他不会改变方向，每次看完手机后他可能继续向前或者回头走。
那么问题来了，已知他在散步过程中始终在 1~N 的范围内，那么符合上述条件的终点可能有多少个呢？
**输入描述**
输入第一行包含一个正整数 N，M，N 表示道路的长度，也是数轴上点的数量，M 是小 A 提供的 D 的数量。(N,M<=20000)
接下来有 M 行，每行一个正整数 D，表示小 A 朝着某个方向走了 D 个单位。(D<=20000)

**输出描述**
输出仅包含一个整数，表示可能的终点的数量。

**输入样例**

```cpp
10 3
5 2 6
```

**输出样例**

```cpp
8
```

**【解题思路】**

用两个 bitset 做标记，然后可以统计答案了。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;

bitset<20005> a, b;

int main() {
    int n, m;
    cin >> n >> m;
    int flag1 = 0;
    int flag = 0;
    a.reset();
    b.reset();
    for (int i = 1; i <= n; i++) {
        a.set(i), b.set(i);
    }
    for (int i = 0; i < m; i++) {
        int l, r;
        scanf("%d", &l);
        a = ((a >> l) | (a << l)) & b;
    }
    for (int i = 1; i <= n; i++) {
        if (a[i] == 1)
            flag1++;
    }
    cout << flag1 << endl;
    return 0;
}
```

### 5、最优加速

【题目描述】跑跑卡丁车是一款经典游戏，这款游戏里面有加速带。已知通过每个加速带，卡丁车会获得一个加速度 a 和加速时间 t。现有 n 个连续码放的加速带，你可以任意调整加速带的顺序，问当加速效果全部结束时，卡丁车最多跑出多远？
说明：
1、加速带效果不可叠加，已知卡丁车的初始速度为 0。
2、所有加速带都是无缝衔接的（即加速带与加速带之间没有缝隙），且小车一开始就被放置于某一加速带上。
3、根据牛顿运动定律，在每个加速带后卡丁车的位移是 L=v0*t+0.5*a*t²;vt=v0+a*t.
**输入描述**
第一行包含一个正整数 n，表示加速带数量(1<=n<=10000)。
接下来 n 行，每行包含两个正整数，a，t，分别表示某一个加速带提供的加速度和加速时间。 (1<=a,t<=1000)
**输出描述**
输出仅包含一个浮点数 L，结果保留一位小数（四舍五入）。

**输入样例**

```cpp
2 2
1
30 2
```

**输出样例**

```cpp
121.0
```

**【解题思路】**

按 a 为第一关键字排序之后，即可统计计算答案了。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;

typedef pair<double, double> pii;

vector<pii> v;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);
    int n;
    cin >> n;
    double tot = 0;
    double vel = 0;
    for (int i = 0; i < n; i++) {
        double a, t;
        cin >> a >> t;
        v.push_back(pii(a, t));
    }
    sort(v.begin(), v.end(), greater<pii>());
    vel = 0;
    for (int i = 0; i < v.size(); i++) {
        tot += vel * v[i].second + 0.5 * v[i].first * v[i].second * v[i].second;
        vel += v[i].first * v[i].second;
    }
    printf("%.1f", tot);
    return 0;
}

```