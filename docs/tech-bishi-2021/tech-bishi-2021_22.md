# 第三章 第 15 节 小红书 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/d4031232d01543ef901a1273fd882965`](https://www.nowcoder.com/tutorial/10028/d4031232d01543ef901a1273fd882965)

# 小红书 2020 秋招笔试真题

![](img/d427695300ecd4b77b74b29c7ad1e0cd.png)

### 1、字符串倒序

【题目描述】薯队长带着小红薯参加密室逃脱团建游戏，首先遇到了反转游戏，小红薯们根据游戏提示收集了多个单词线索，并将单词按要求加一个空格组成了句子，最终要求把句子按单词反转解密。
说明：收集的时候单词前后可能会有多个空格，反转后单词不能有多个空格，具体见输入输出样例。
**输入描述**
输入一个字符串。包含空格和可见字符。长度<=10000。
**输出描述**
输出一个字符串，表示反转后结果。
**输入样例**

```cpp
the sky is blue!
```

**输出样例**

```cpp
blue! is sky the
```

**【解题思路】**

字符串操作。根据题意实现即可

**【参考代码】**

```cpp
import java.util.*;
import java.lang.*;

public class Main {
    public static void getReverseSentence(String str) {
        String[] s = new String(str).split("\\s+");
        if (s.length == 0) {
            System.out.println(" ");
        } else if (s.length == 1) {
            System.out.println(s[0]);
        } else {
            System.out.print(s[s.length - 1]);
            for (int i = s.length - 2; i >= 0; i--) {
                System.out.print(" " + s[i]);
            }
        }
    }
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String str = sc.nextLine();
        getReverseSentence(str);
    }
}
```

### 2、笔记精选

【题目描述】薯队长写了 n 篇笔记， 编号从 1～n，每篇笔记都获得了不少点赞数。 薯队长想从中选出一些笔记，作一个精选集合。挑选的时候有两个规则：
1\. 不能出现连续编号的笔记。
2\. 总点赞总数最多
如果满足 1，2 条件有多种方案，挑选笔记总数最少的那种。
**输入描述**输入包含两行。第一行整数 n 表示多少篇笔记。
第二行 n 个整数分别表示 n 篇笔记的获得的点赞数。（0<n<=1000, 0<=点赞数<=1000)
**输出描述**输出两个整数 x y。空格分割。
x 表示总点赞数，y 表示挑选的笔记总数。
**输入样例**

```cpp
4 1
2 3 1
```

**输出样例**

```cpp
4 2
```

**【解题思路】**

扫描一次，满足不相邻且总赞数更多更新状态。

**【参考代码】**

```cpp
import java.util.Scanner;
import java.util.Stack;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int optimalSum = 0;
        int excludeSum = 0;
        int optimalCount = 0;
        int excludeCount = 0;
        for (int i = 0; i < n; i++) {
            int like = scanner.nextInt();
            int nextOptimalSum = optimalSum;
            int nextOptimalCount = optimalCount;
            if (excludeSum + like > optimalSum
                    || (excludeSum + like == optimalSum && excludeCount + 1 < optimalCount)) {
                nextOptimalSum = excludeSum + like;
                nextOptimalCount = excludeCount + 1;
            }
            excludeSum = optimalSum;
            excludeCount = optimalCount;
            optimalSum = nextOptimalSum;
            optimalCount = nextOptimalCount;

        }
        System.out.println(String.format("%s %s", optimalSum, optimalCount));
    }
}
```

### 3、击败魔物

【题目描述】薯队长来到了迷宫的尽头，面前出现了 N 只魔物，H[i]表示第 i 只魔物的血量，薯队长需要在 T 个回合内击败所有魔物才能获胜。每个回合薯队长可以选择物理攻击一只魔物，对其造成 1 点伤害（物理攻击次数无上限）; 或者消耗 1 点法力释放必杀技对其造成固定 X 点伤害（薯队长开始拥有 M 点法力）。问 X 至少多大，薯队长才有机会获胜；如果无论如何都无法在 T 回合内获胜，则输出-1
**输入描述**
第一行三个整数分别表示：N T M
第二行有 N 个整数：H[1] H[2] H[3] … H[N]
对于 50%的数据：
0 < N < 10³
0 < T < 10³
0 <= M <= T
0 < H[i] < 10⁴
对于 100%的数据
0 < N < 10⁵
0 < T < 10⁷
0 <= M <= T
0 < H[i] < 10⁷

**输出描述**
输出一个整数，表示必杀技一次最少造成多少固定伤害

**输入样例**

```cpp
3 4 3
5 2 1
```

**输出样例**

```cpp
3
```

**【解题思路】**

二分答案，贪心的进行判断是否可行。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;

bool check(int x, int n, int t, int m, vector<int> arr) {
    for (int i = 0; i < n; ++i) {
        if (m <= 0 || t <= 0) {
            break;
        }
        if (arr[i] < x) {
            continue;
        }
        int hit = min(arr[i] / x, m);
        hit = min(hit, t);
        m -= hit;
        arr[i] -= hit * x;
        t -= hit;
    }
    if (t <= 0) {
        for (int i = 0; i < n; ++i) {
            if (arr[i] > 0) {
                return false;
            }
        }
        return true;
    }
    sort(arr.begin(), arr.end(), greater<int>());
    for (int i = 0; i < n; ++i) {
        if (arr[i] <= 0) {
            break;
        }
        if (t < 0)
            break;
        if (m > 0) {
            m--;
            t--;
        } else {
            t -= arr[i];
        }
    }
    return t >= 0;
}
int main() {
    int n, t, m, h;
    cin >> n >> t >> m;
    vector<int> arr;
    for (int i = 0; i < n; ++i) {
        cin >> h;
        arr.push_back(h);
    }
    sort(arr.begin(), arr.end(), greater<int>());
    int right = arr[0];
    int left = 0;
    int ans = -1;
    while (left < right) {
        int mid = (left + right) >> 1;
        if (check(mid, n, t, m, arr)) {
            right = mid;
            ans = mid;
        } else {
            left = mid + 1;
        }
    }
    cout << ans << endl;
    return 0;
}
```

### 4、笔试草稿

【题目描述】薯队长写了一篇笔记草稿，请你帮忙输出最后内容。
1.输入字符包括英文字符，"(" , ")" 和 "<"。
2.英文字符表示笔记内容。
3\. ( ) 之间表示注释内容，任何字符都无效。 括号保证成对出现。
4\. "<" 表示退格, 删去前面一个笔记内容字符。 括号不受 "<" 影响 。
**输入描述**
输入一行字符串。长度<=10000。
**输出描述**
输出一行字符串，表示最终的笔记内容。
**输入样例**

```cpp
a<<b((c)<)
```

**输出样例**

```cpp
b
```

**【解题思路】**

用栈来实现，类似表达式求值。

**【参考代码】**

```cpp
import java.util.Scanner;
import java.util.Stack;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String s = scanner.nextLine();
        Stack<Character> stack = new Stack<>();
        for (char c : s.toCharArray()) {
            switch (c) {
                case '<':
                    if (!stack.isEmpty() && stack.peek() != '(') {
                        stack.pop();
                    }
                    break;
                case ')':
                    while (stack.peek() != '(') {
                        stack.pop();
                    }
                    stack.pop();
                    break;
                default:
                    stack.push(c);
            }
        }
        StringBuilder sb = new StringBuilder();
        while (!stack.isEmpty()) {
            sb.append(stack.pop());
        }
        System.out.println(sb.reverse().toString());
    }
}
```

### 5、倒卖战利品

【题目描述】在游戏中，击败魔物后，薯队长获得了 N 件宝物，接下来得把这些宝物卖给宝物回收员来赚点小钱。这个回收员有个坏毛病，每次卖给他一件宝物后，之后他就看不上比这件宝物差的宝物了。在这个世界中，衡量宝物的好坏有两个维度，稀有度 X 和实用度 H，回收员在回收一个宝物 A 后，下一个宝物的稀有度和实用度都不能低于宝物 A。那么薯队长如何制定售卖顺序，才能卖给回收员宝物总个数最多。
**输入描述**
第一行一个正整数 N。
接下来 N 行。每行两个整数分别表示 X 和 H
X[1] H[1]
X[2] H[2]
… X
N H[N]**输入限制**
对于 70%的数据：
0<N<0⁴
0<X[i]<10⁶
0<H[i]<10⁶
对于 100%的数据：
0<N<10⁶
0<X[i]<10⁶
0<H[i]<10⁶
**输出描述**
一个整数，表示最多可以卖出的宝物数
**输入样例**

```cpp
4 3
2
1 1
1 3
2 2
```

**输出样例**

```cpp
3
```

**【解题思路】**

经典动态规划。最长上升子序列。

**【参考代码】**

```cpp
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Main {
    static class Pair {
        int x;
        int y;

        Pair(int x, int y) {
            this.x = x;
            this.y = y;
        }

    }
    public static int lengthOfLIS(int[] nums) {

        List<Integer> result = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int low = 0;
            int upper = result.size();
            while (low < upper) {
                int mid = (upper - low) / 2 + low;
                if (result.get(mid) <= nums[i]) {
                    low = mid + 1;
                } else {
                    upper = mid;
                }

            }
            if (upper == result.size()) {

                result.add(nums[i]);
            } else {

                result.set(upper, nums[i]);
            }

        }
        return result.size();

    }
    public static void main(String[] args) {

        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        List<Pair> data = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            data.add(new Pair(Integer.parseInt(in.next()), Integer.parseInt(in.next())));
        }
        data.sort((d1, d2) -> {
            if (d1.x < d2.x || d1.x == d2.x && d1.y < d2.y) {
                return -1;
            } else if (d1.x > d2.x || d1.x == d2.x && d1.y > d2.y) {
                return 1;
            } else {
                return 0;
            }

        });
        System.out.println(lengthOfLIS(data.stream().mapToInt(d -> d.y).toArray()));
    }
}

```