# 第三章 第 11 节 哔哩哔哩 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/5e5ace9d6f3a4677b66f974ba53d8f57`](https://www.nowcoder.com/tutorial/10028/5e5ace9d6f3a4677b66f974ba53d8f57)

# 哔哩哔哩 2020 秋招笔试真题

![](img/45b79863ff8bbff0fc1cde39a52bedbc.png)

### 1、连续整数求和

【题目描述】给定一个正整数 N，试求有多少组连续正整数满足所有数字之和为 N? (1<= N <= 10⁹)

**输入描述**

```cpp
5
```

**输出描述**

```cpp
2
```

**备注**

解释: 5=5=2+3，共有两组连续整数([5],[2,3])求和后为 5。

**示例 1**

**输入**

```cpp
5
```

**输出**

```cpp
2
```

**说明**

5=5=2+3，共有两组连续整数([5],[2,3])求和后为 5。

**【解题思路】**

直接枚举判断即可。

**【参考代码】**

```cpp
#include <iostream>
using namespace std;
int consecutiveNumbersSum(int N) {
    int res = 1;
    int temp = 0;
    for (int n = 2; n < N; n++) {
        temp = temp + n - 1;
        if (temp >= N) {
            break;
        }
        if (temp % n == N % n) {
            res++;
        }
    }
    return res;
}
int main() {
    int n;
    cin >> n;
    cout << consecutiveNumbersSum(n);
    return 0;
}
```

### 2、复数乘法

【题目描述】输入两个表示复数的字符串，输出它们相乘的结果的字符串

复数字符串用 a+bi 表示(a,b 为整数,i 为虚数单位 i²=1)

**输入描述**

两个表示复数的字符串

**输出描述**

两个数相乘的结果的字符串

**示例 1**

**输入**

```cpp
1+2i
2+1
```

**输出**

```cpp
0+5i
```

**说明**

(1+2i)(2+i)=(2+i+4i+2i*i)=0+5i

**示例 2**

**输入**

```cpp
1+-2i
3+4i
```

**输出**

```cpp
11+-2i
```

**说明**

(1+-2i)(3+4i)=(3+4i-6i-8i*i)=11+-2i

**【解题思路】**

模拟实现复数运算。

**【参考代码】**

```cpp
#include <iostream>
#include <string>
using namespace std;

void par***plex(string c, int *r, int *v) {
    int p = 0, l = c.length();
    for (int i = 0; i < l; i++) {
        if (c[i] == '+') {
            p = i;
            break;
        }
    }
    *r = stoi(c.substr(0, p));
    *v = stoi(c.substr(p + 1, l - 1));
}

int main() {
    string a, b;
    cin >> a >> b;
    int r1, v1, r2, v2;
    par***plex(a, &r1, &v1);
    par***plex(b, &r2, &v2);
    int r = r1 * r2 - v1 * v2;
    int v = r1 * v2 + r2 * v1;
    cout << r << "+" << v << "i";
    return 0;
}
```

### 3、数组排成最小的数

【题目描述】输入一个正整数数组，将它们连接起来排成一个数，输出能排出的所有数字中最小的一个。

**输入描述**

一行输入，数组中的数字用逗号隔开。例如：
输入为：
32,231
则表示数组{32, 231}

**输出描述**

直接输出最小数字即可，如示例题目中，输出为：
23132

**示例 1**

**输入**

```cpp
32,231
```

**输出**

```cpp
23132
```

**【解题思路】**

经典贪心。对所有字符串按照 A+B<B+A 的规则排序，然后按着拼接即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

int compare(const void *strNumber1, const void *strNumber2) {
    const int MaxNumberLength = 10;
    char *strCombine1 = new char[MaxNumberLength * 2 + 1];
    char *strCombine2 = new char[MaxNumberLength * 2 + 1];

    strcpy(strCombine1, *(const char **)strNumber1);
    strcat(strCombine1, *(const char **)strNumber2);

    strcpy(strCombine2, *(const char **)strNumber2);
    strcat(strCombine2, *(const char **)strNumber1);

    int result = strcmp(strCombine1, strCombine2);

    delete[] strCombine1;
    delete[] strCombine2;

    return result;
}

void PrintMinNumber(int *numbers, int length) {
    const int MaxNumberLength = 10;
    if (numbers == NULL || length <= 0)
        return;
    char **strNumbers = (char **)(new char *[length]);
    for (int i = 0; i < length; ++i) {
        strNumbers[i] = new char[MaxNumberLength + 1];
        sprintf(strNumbers[i], "%d", numbers[i]);
    }
    qsort(strNumbers, length, sizeof(char *), compare);

    for (int i = 0; i < length; ++i)
        printf("%s", strNumbers[i]);
    printf("\n");

    for (int i = 0; i < length; ++i)
        delete[] strNumbers[i];
    delete[] strNumbers;
}
int main() {
    std::string str;
    std::cin >> str;
    std::string pattern = ",";
    str = str + pattern;
    std::string::size_type pos;
    std::vector<int> result;
    int size = str.size();
    for (int i = 0; i < size; i++) {
        pos = str.find(pattern, i);
        if (pos < size) {
            std::string s = str.substr(i, pos - i);
            result.push_back(atoi(s.c_str()));
            i = pos + pattern.size() - 1;
        }
    }

    PrintMinNumber(result.data(), result.size());
}
```

### 4、找出有序数组中和为 sum 的两个数

【题目描述】找出有序数组（从小到大排列）中和为 sum 的两个数，要求复杂度为 O(n)，找到一组即可

**输入描述**

第一行：数组长度
第二行：数组各项的值
第三行：sum

**输出描述**

若存在，输出和为 sum 的两个数，以空格分隔；若不存在，输出 notfound

**示例 1**

**输入**

```cpp
5
1 3 4 6 8
10
```

**输出**

```cpp
4 6
```

**示例 2**

**输入**

```cpp
5
1 3 4 6 8
13
```

**输出**

```cpp
notfound
```

**【解题思路】**

双指针维护一前一后，然后根据和值调整指针移动。

**【参考代码】**

```cpp
#include <iostream>
using namespace std;

int main() {
    int n, sum;
    cin >> n;
    int input[n];
    for (int i = 0; i < n; i++) {
        cin >> input[i];
    }
    cin >> sum;

    int p1 = 0, p2 = n - 1;
    while (p1 < p2) {
        int s = input[p1] + input[p2];
        if (s == sum) {
            cout << input[p1] << ' ' << input[p2];
            return 0;
        }
        if (s < sum) {
            p1++;
        } else {
            p2--;
        }
    }
    cout << "notfound";
    return 0;
}
```

### 5、最小操作次数

【题目描述】给出两个单词 word1 和 word2，计算出将 word1 转换为 word2 的最少操作次数。

你总共三种操作方法：

插入一个字符

删除一个字符

替换一个字符

**输入描述**

一共两行，分别代表 word1 和 word2

**输出描述**

直接打印次数即可

**示例 1**

**输入**

```cpp
abc
abd
```

**输出**

```cpp
1
```

**说明**

把 c->d，只需要一次操作

**【解题思路】**

经典动态规划。字符串最小编辑距离。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int word_change(string word1, string word2) {
    int n = word1.length();
    int m = word2.length();
    vector<vector<int>> dp(n + 1, vector<int>(m + 1));
    for (int i = 1; i <= n; i++)
        dp[i][0] = i;
    for (int i = 1; i <= m; i++)
        dp[0][i] = i;
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            if (word1[i - 1] == word2[j - 1])
                dp[i][j] = dp[i - 1][j - 1];
            else
                dp[i][j] =
                    1 + min(dp[i][j - 1], min(dp[i - 1][j], dp[i - 1][j - 1]));
        }
    }
    return dp[n][m];
}
int main() {
    string s1, s2;
    cin >> s1 >> s2;
    cout << word_change(s1, s2) << endl;
}

```