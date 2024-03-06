# 第三章 第 7 节 京东 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/7ac38f87541547b69e812427b3303e93`](https://www.nowcoder.com/tutorial/10028/7ac38f87541547b69e812427b3303e93)

# 京东 2020 秋招笔试真题

![](img/c373996c9da2519df402caaa6c26c4cc.png)

### 1、最优打字策略

【题目描述】在英文的输入中，我们经常会遇到大小写切换的问题，频繁切换大小写会增加我们的按键次数，也会降低我们的打字效率。

众所周知，切换大小写有两种方式，一种是按下“caps locks”，也就是大写锁定键，这样一来，之后的输入模式都会被切换。另一种是同时按下 shift 和需要打印的字母，可以临时切换大小写（算作按下两个键）。

已知初始状态下，打字模式是小写，现在给出需要打印的字符串（区分大小写），请你计算出最少需按键多少次才能打印出来。

**输入描述：**

输入第一行仅包含一个正整数 n，表示字符串的长度（1<=n<=1000000）。

输入第二行包含一个长度为 n 的字符串，仅包含大小写字母。

**输出描述：**

输出仅包含一个正整数，即最少的按键次数。

**输入样例：**

```cpp
6
AaAAAA
```

**输出样例：**

```cpp
8
```

**【解题思路】**

1.遍历字符串，判断连续 2 个输入的状态

2.只在本次输入为大写且未锁定状态下按键次数 times++，如果下一个输入为大写，就让本次按键为 lock，修改 lock 标记为 true，如果下一个输入为小写，让本次按键为 shift，保持 lock 为 false 状态；

3\. 同样的，只有当输入为小写且锁定状态，按键次数 times++，如果下一个输入为小写，就让本次按键为 lock，修改 lock 标记为 false，如果下一个输入为大写，让本次按键为 shift，保持 lock 为 true 状态

**【参考代码】**

```cpp
#include <bit/stdc++.h>
char a[1000005];
int main() {
    int n, t;
    scanf("%d", &n);
    getchar();
    scanf("%s", a, 201);
    int len = strlen(a), k = 0;
    int sum = 0;
    for (int i = 0; i < len; i++) {
        sum++;
        if (k == 0) {
            if (a[i] >= 'A' && a[i] <= 'Z') {
                sum++;
                if (a[i + 1] >= 'A' && a[i + 1] <= 'Z')
                    k = 1;
            }
        }
        if (k == 1) {
            if (a[i] >= 'a' && a[i] <= 'z') {
                sum++;
                if (a[i + 1] >= 'a' && a[i + 1] <= 'z')
                    k = 0;
            }
        }
    }
    printf("%d\n", sum);
    return 0;
}
```

### 2、消消乐

【题目描述】消消乐是当下十分火爆的一个脑力游戏。

游戏是这样的，有一个 5*5 的正方形网格，每个格子中有一个大于 0 且小于 4 的整数，对于一个确定的局面，若一个格子与它上下左右四个方向的某个格子（如果存在） 数字相同，则称这两个格子是连通的，并且这种连通具有传递性。

每次，你可以选择一个格子，若与这个格子连通的格子（包括自己）数大于等于 3，你就可以选择消掉这个格子，与此同时，与这个格子连通的所有格子会一起消失。

如果仅仅是这样，那太简单了，因为无论如何消，最后的结果都是一样的，所以我们引入了重力系统，每次选择消掉某个格子，并将与那个格子相连通的所有格子都消掉后将会有一些格子失去支撑，此时那些格子就会因重力而下落。

那么怎样玩才能使得最后剩下的不能消掉的格子尽量少。

样例解释：

31211      3x2xx      xxxxx      xxxxx      xxxxx

11113       xxxx3      xxxxx      xxxxx      xxxxx

11111       xxxxx      xxxxx      xxxxx       xxxxx

11111       xxxxx      3x2x3      3xxx3      3xxxx

31222     3x222      3x222      3xxxx      3xxx3

X 表示空缺，每个连通块用相同的颜色标记，此过程演示样例

**输入描述**

一个 5*5 的矩阵描述正方形网格，数字之间用空格隔开。

**输出描述**

一个数表示最后剩下的不能消掉的格子最少是多少。

**输入样例**

```cpp
3 1 2 1 1
1 1 1 1 3
1 1 1 1 1
1 1 1 1 1
3 1 2 2 2
```

**输出样例**

```cpp
3
```

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
const int fx[5] = {0, -1, 1, 0, 0};
const int fy[5] = {0, 0, 0, -1, 1};
int b[6][6], a[7][7], c[6][6], i, j, ans, p, q;

int flood(int x, int y) {
    int i, xx, yy, ans = 0, color = a[x][y];
    ans++;
    b[x][y] = true;
    a[x][y] = 0;
    for (i = 1; i <= 4; i++) {
        xx = x + fx[i];
        yy = y + fy[i];
        if ((a[xx][yy] == color) && (!b[xx][yy]))
            ans += flood(xx, yy);
    }
    a[x][y] = color;
    return ans;
}
void fill(int x, int y) {
    int i, xx, yy, color = a[x][y];
    a[x][y] = 0;
    for (i = 1; i <= 4; i++) {
        xx = x + fx[i];
        yy = y + fy[i];
        if (a[xx][yy] == color)
            fill(xx, yy);
    }
}
void fall() {
    int k;
    for (i = 1; i <= 5; i++)
        for (j = 4; j >= 1; j--)
            if (a[j][i] != 0) {
                k = j;
                while ((k <= 4) && (a[k + 1][i] == 0)) {
                    a[k + 1][i] = a[k][i];
                    a[k][i] = 0;
                    k++;
                }
            }
}
void work() {
    int k, i, cant = 0, can = 0, c[6][6], d[6][6];
    memset(b, false, sizeof(b));
    for (p = 1; p <= 5; p++)
        for (q = 1; q <= 5; q++)
            c[p][q] = a[p][q];
    for (i = 1; i <= 5; i++)
        for (j = 1; j <= 5; j++)
            if ((!b[i][j]) && (a[i][j] > 0)) {
                k = flood(i, j);
                if (k <= 2) {
                    cant += k;
                    continue;
                }
                can++;
                fill(i, j);
                fall();
                for (p = 1; p <= 5; p++)
                    for (q = 1; q <= 5; q++)
                        d[p][q] = b[p][q];
                work();
                for (p = 1; p <= 5; p++)
                    for (q = 1; q <= 5; q++)
                        a[p][q] = c[p][q];
                for (p = 1; p <= 5; p++)
                    for (q = 1; q <= 5; q++)
                        b[p][q] = d[p][q];
            }
    if ((can == 0) && (cant < ans))
        ans = cant;
}
int main() {
    for (i = 1; i <= 5; i++)
        for (j = 1; j <= 5; j++)
            scanf("%d", &a[i][j]);
    ans = 1000000;
    work();
    printf("%d", ans);
    return 0;
}
```

### 3、合唱队形

【题目描述】合唱队的 N 名学生站成一排且从左到右编号为 1 到 N，其中编号为 i 的学生身高为 H[i]。现在将这些学生分成若干组（同一组的学生编号连续），并让每组学生从左到右按身高从低到高进行排列，使得最后所有学生同样满足从左到右身高从低到高（中间位置可以等高），那么最多能将这些学生分成多少组？

**输入描述**

第一行包含一个整数 N，1≤N≤10⁵。

第二行包含 N 个空格隔开的整数 H1 到 HN，1≤H[i]≤10⁹。

**输出描述**

输出能分成的最多组数。

**输入样例**

```cpp
4
2 1 3 2
```

**输出样例**

```cpp
2
```

**【解题思路】**

贪心。b[i]表示 i 之后出现的最小值，然后对于每个 a[i]进行二分查找出最优构成分组的地方。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
#define N 100007
using namespace std;
int n, a[N], b[N];
int find(int l, int r, int x) {
    int res = r + 1;
    while (l <= r) {
        int mid = (l + r) >> 1;
        if (b[mid] >= x)
            res = mid, r = mid - 1;
        else
            l = mid + 1;
    }
    return res;
}
int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n; ++i)
        scanf("%d", &a[i]);
    b[n] = a[n];
    for (int i = n - 1; i; --i)
        b[i] = min(a[i], b[i + 1]);
    int ans = 0;
    for (int i = 1, j = 1; i <= n; ++i) {
        if (i == j)
            ++ans;
        j = max(j, find(i + 1, n, a[i]));
    }
    printf("%d", ans);
}
```

### 4、姓名排序

【题目描述】马上就要开学了，教务处的老师拿到了新生的名单，现在他需要根据考生的姓名录入一个拼音版的新名单。

老师录入时，需要输入姓和名(例如：ZHANG SAN，字母均为大写，姓名以空格隔开)，并且要将这些人按一定规则排序。

排序的方式如下：

首先，按照该姓的出现次数排序，即：姓出现次数多的人先排序；

其次，若两个人的姓出现的次数一样多（或者是同一个姓），按照原名单的顺序。

**输入描述**

输入包括多行，每一行两个字符串，代表一个人的姓和名

**输出描述**

输出排序后的名单

**输入样例**

```cpp
ZHANG SAN
LI SI
WANG WU
WANG LIU
WANG QI
ZHANG WU
LI WU
```

**输出样例**

```cpp
WANG WU
WANG LIU
WANG QI
ZHANG SAN
LI SI
ZHANG WU
```

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

struct node {
    string xing;
    string ming;
    int pop;
    int idx;
};
node stu[50010];
int n, i, j, num;
char x[60], m[60];

bool comp1(const node &a, const node &b) { return a.xing < b.xing; }
bool comp2(const node &a, const node &b) {
    if (a.pop != b.pop)
        return a.pop > b.pop;
    if (a.idx != b.idx)
        return a.idx < b.idx;
}
int main() {
    n = 1;
    while (cin >> stu[n].xing >> stu[n].ming) {
        stu[n].idx = n;
        stu[n].pop = 1;
        n++;
    }
    sort(stu + 1, stu + n + 1, comp1);
    num = 1;
    for (i = 2; i <= n; i++) {
        if (stu[i].xing != stu[i - 1].xing) {
            for (int j = num; j < i; j++)
                stu[j].pop = i - num;
            num = i;
        } else if (i == n)
            for (int j = num; j <= i; j++)
                stu[j].pop = i - num + 1;
    }
    sort(stu + 1, stu + n + 1, comp2);

    for (i = 1; i <= n; i++) {
        cout << stu[i].xing << " " << stu[i].ming << endl;
    }
    return 0;
}
```

### 5、拼接迷宫

【题目描述】构造一个迷宫是很麻烦的一件事情，因此有人提出了一种迷宫生成方法，与铺砖的方法类似，首先设计一个 n*m 的单位迷宫，然后就像铺砖一样，将这个单位迷宫复制拼接起来，如果能够通过这种方式生成的迷宫可以从起始位置通向无穷多的位置，那么我们认为这个单位迷宫是合法的（每个单位不可旋转）。

单位迷宫的表示包含三种符号,‘#’,‘.’和‘S’，其中‘#’代表墙，‘.’代表没有障碍物可以通过的，S 则代表的是起始位置，当然迷宫是只有一个起点的，你可以任选一个单位迷宫的 S 位置作为起点，其他单位迷宫的 S 则视为可通行的。

**输入描述**

输入第一行包含一个正整数 T，表示数据组数。(1<=T<=10)

接下来有 T 组输入，每组输入第一行是两个正整数 n,m,表示单位迷宫是 n 行 m 列（1<=n,m<=300）。

之后 n 行每行是一个长度为 m 的字符串。如题所述，描述了一个单位迷宫。

**输出描述**

对于每组输入输出一行，如果这个单位迷宫是合法的，就输出“Yes”，否则就输出“No”。

**输入样例**

```cpp
2
2 2
S#
#.
3 3
...
###
#S#
```

**输出样例**

```cpp
No
Yes
```

**【解题思路】**

从每个起始位置出发开始搜索，如果横纵坐标模行列长度之后出现重复但坐标不一样了则可以无穷下去。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
#define N 1510
#define For(i, x, y) for (i = x; i <= y; i++)
using namespace std;
const int a1[4] = {1, -1, 0, 0}, a2[4] = {0, 0, 1, -1};
struct ww {
    int x, y;
} f[N][N];
int i, j, k, n, m;
int v[N][N];
char p[N][N];
bool dfs(int x, int y) {
    int A = (x % n + n) % n, B = (y % m + m) % m;
    if (p[A][B] == '#')
        return 0;
    if (v[A][B])
        return f[A][B].x != x || f[A][B].y != y;
    v[A][B] = 1;
    f[A][B] = (ww){x, y};
    int i;
    For(i, 0, 3) if (dfs(x + a1[i], y + a2[i])) return 1;
    return 0;
}
int main() {
    int t;
    scanf("%d", &t);
    while (t--) {
        memset(v, 0, sizeof(v));
        memset(f, 0, sizeof(f));
        scanf("%d%d", &n, &m);
        For(i, 0, n - 1) scanf("%s", p[i]);
        For(i, 0, n - 1) For(j, 0, m - 1) if (p[i][j] == 'S') {
            p[i][j] = '.';
            printf("%s\n", dfs(i, j) ? "Yes" : "No");
            break;
        }
    }
    return 0;
}
```