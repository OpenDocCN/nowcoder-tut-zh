# 第三章 第 12 节 爱奇艺 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/d9133df2de41439fb70933065acbf8bf`](https://www.nowcoder.com/tutorial/10028/d9133df2de41439fb70933065acbf8bf)

#  爱奇艺 2020 秋招笔试真题

![](img/bc45c035761e3e4eed774bddfca49d71.png)

### 1、切割块

【题目描述】有一个 x*y*z 的立方体，要在这个立方体上砍 k 刀，每一刀可以看作是用一个平行于立方体某一面的平面切割立方体，且必须在坐标为整数的位置切割，如在 x=0.5 处用平面切割是非法的。

问在切割 k 刀之后，最多可以把立方体切割成多少块。

**输入描述**

输入仅包含一行，一行包含 4 个正整数 x,y,z,k 分别表示 x*y*z 的立方体和切割 k 刀。（1<=x,y,z<=10⁶,0<=k<=10⁹）

**输****出****描述**

输出仅包含一个正整数，即至多切割成多少块。

**输入****样例 1**

```cpp
2 2 2 3
```

**输****出样例 1**

```cpp
8
```

**【解题思路】**

由于必须按整数来切，所以

max 块=x*y*z，实际就是体积的大小

max 刀=(x-1)+(y-1)+(z-1)，就是保证切出来的==立方体体积最小=1==边长为 3 就要切 2 刀，2*2*2* 切 3 刀=8 块，2*2*2* 切 2 刀=几块？

这需要在某一个边上少切一刀，是那一条边？（实践证明是最长的那条边）

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int a[5], k;
    long long int maxd, maxk, m = 104909296875;
    scanf("%d%d%d%d", &a[1], &a[2], &a[3], &k);
    //刀数
    maxd = (long long int)(a[1] - 1) + (a[2] - 1) + (a[3] - 1);
    //快数
    maxk = (long long int)a[1] * a[2] * a[3];
    // k 超过刀数
    if (k >= maxd) {
        printf("%lld\n", maxk);
        return 0;
    } else
        while (maxd != k) {
            //找最长的那条边
            sort(a + 1, a + 4);
            //最长边减 1
            a[3]--;
            //新的刀数和快数
            maxd = (long long int)(a[1] - 1) + (a[2] - 1) + (a[3] - 1);
            maxk = (long long int)a[1] * a[2] * a[3];
        }
    printf("%lld", maxk);
    return 0;
}
```

### 2、谁当裁判

【题目描述】假设有 N 个人要玩游戏，每轮游戏必须选出一个人当裁判，剩下的 N-1 个人作为玩家。现在第 i 个人要求作为玩家进行至少 A[i]轮游戏，那么至少需要进行多少轮游戏才能满足所有人的要求？

**输入描述**

第一行包含一个整数 N，2≤N≤10⁵。

第二行包含 N 个空格隔开的整数 A[1]到 A[N]，0≤A[i]≤10⁹。

**输出描述**

输出至少需要进行的游戏轮数。

**输入样例**

```cpp
3
2 2 3
```

**输出样例**

```cpp
4
```

**【解题思路】**

和值减去最大值即为答案。

**【参考代码】**

```cpp
N=int(input().strip())
A=list(map(int,input().strip().split()))
print(sum(A)-max(A))
```

### 3、排列计数

【题目描述】给定一个大小为 N-1 且只包含 0 和 1 的序列 A[1]到 A[N-1]，如果一个 1 到 N 的排列 P[1]到 P[N]满足对于 1≤i<N，当 A[i]=0 时 P[i]<P[i+1]，当 A[i]=1 时 P[i]>P[i+1]，则称该排列符合要求，那么有多少个符合要求的排列？

**输入描述**

第一行包含一个整数 N，1<N≤1000。

第二行包含 N-1 个空格隔开的整数 A[1]到 A[N-1]，0≤A[i]≤1。

**输出描述**

输出符合要求的排列个数对 10⁹+7 取模后的结果。

**输入样例**

```cpp
4
1 1
```

**输出样例**

```cpp
3
```

**样例解释**

符合要求的排列为{3 2 1 4}、{4 2 1 3}和{4 3 1 2}。

**【解题思路】**

根据数组中的值，对应 dp 即可。

**【参考代码】**

```cpp
def main():
    mod = 1000000007
    N = int(input())
    flags = list(map(int, input().strip().split()))
    dp = [[0 for _ in range(N+1)] for _ in range(N)]
    for i in range(N):
        dp[0][i] = 1

    for i in range(1, N):
        if flags[i-1] == 1:
            for j in range(N-i-1, -1, -1):
                dp[i][j] += dp[i-1][j+1] + dp[i][j+1]
                dp[i][j] %= mod
        else:
            dp[i][0] += dp[i-1][0]
            dp[i][0] %= mod
            for j in range(1, N-i):
                dp[i][j] += dp[i-1][j] + dp[i][j-1]
                dp[i][j] %= mod
    # print(dp)
    print(dp[N-1][0] % mod)

if __name__ == '__main__':
main()
```

### 4、最强大脑

【题目描述】人脑对于长度特别长的字符串的处理速度是有限的，但是最强大脑挑战的就是人脑的极限，现在有这样一项挑战，给出一个很长的字符串 S，和一个较短的字符串 T，请你求出对于每一个前缀[1,r]内有多少个 T 字符串。

**输入描述**

第一行一个字符串 S。

第二行一个字符串 T。两个字符串保证均只含小写字母。（1≤|S|≤500000, 1≤|T|≤100）

**输****出****描述**

输出仅包含|S|个正整数，分别表示[1,r]内有多少个 T 字符串。(1<=r<=|S|)

**输入****样例 1**

```cpp
ababac
ab
```

**输****出样例 1**

```cpp
0 1 1 2 2 2
```

**【解题思路】**

字符串匹配问题，注意本题子串可以有重叠。

**【参考代码】**

```cpp
import sys

def countsubstr(s, t):
    pre = 0
    ans = []
    for i in range(len(s)):
        if i >= len(t) - 1:
            if s[i-len(t)+1:i+1] == t:
                pre += 1
        ans.append(pre)
    return ans

if __name__ == '__main__':

    lines = sys.stdin.readlines()
    if len(lines) == 1:
        s = lines[0].strip()
        t = ''
    else:
        s = lines[0].strip()
        t = lines[1].strip()

    if len(t) > len(s):
        for i in range(len(s)):
            print(0, end=' ')
    elif not t&nbs***bsp;len(t) == 0:
        for i in range(len(s)):
            print(0, end=' ')
    else:
        ans = countsubstr(s, t)
        for i in range(len(s)):
            print(ans[i], end=' ')
```

### 5、红篮球

【题目描述】有一个非常经典的概率问题，是一个袋子里面有若干个红球和若干个蓝球，两个人轮流取出一个球，谁先取到红球谁就赢了，当人的先后顺序和球的数量确定时，双方的胜率都可以由计算得到，这个问题显然是很简单的。

现在有一个进阶版的问题，同样是一个袋子里面有 n 个红球和 m 个蓝球，共有 A,B,C 三人参与游戏，三人按照 A,B,C 的顺序轮流操作，在每一回合中 A,B,C 都会随机从袋子里面拿走一个球，然而真正分出胜负的只有 A,B 两个人，没错，C 就是来捣乱的，他除了可以使得袋子里面减少一个球，没有其他任何意义，而 A,B 谁先拿到红球就可以获得胜利，但是由于 C 的存在，两人可能都拿不到红球，此时 B 获得胜利。

**输入描述**

输入仅包含两个整数 n 和 m,表示红球和蓝球的数量，中间用空格隔开。(0<=n,m<=1000)

**输出描述**

请你输出 A 获胜的概率，结果保留 5 位小数。

**输入样例****1**

```cpp
1 1
```

**输出样例****1**

```cpp
0.50000
```

**输入样例 2**

```cpp
3 4
0.62857
```

**【解题思路】**

概率 dp。dp[i][j]表示 i 个红球和 j 个蓝球时的 A 获胜概率。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

double process(int n, int m) {
    vector<vector<double>> dp(n + 1, vector<double>(m + 1));
    for (int i = 1; i <= n; i++)
        dp[i][0] = 1;
    for (int i = 0; i <= m; i++)
        dp[0][i] = 0;
    for (int i = 1; i <= n; ++i) {
        for (int j = 1; j <= m; ++j) {
            const double di = i, dj = j, dij = di + dj;
            if (i + j < 3 || j <= 1)
                dp[di][dj] = di / dij;
            else if (j < 3)
                dp[i][j] = di / dij + dj / dij * (dj - 1) / (dij - 1) * di /
                                          (dij - 2) * dp[i - 1][j - 2];
            else
                dp[i][j] = di / dij +
                           dj / dij * (dj - 1) / (dij - 1) * (dj - 2) /
                               (dij - 2) * dp[i][j - 3] +
                           dj / dij * (dj - 1) / (dij - 1) * di / (dij - 2) *
                               dp[i - 1][j - 2];
        }
    }
    return dp[n][m];
}

int main() {
    int n, m;
    cin >> n >> m;
    double res = 0;
    if (n == 0 && m == 0) {
        res = 0;
    } else if (n == 0) {
        res = 0;
    } else if (m == 0) {
        res = 1;
    } else {
        res = process(n, m);
    }
    printf("%.5lf\n", res);
}
```