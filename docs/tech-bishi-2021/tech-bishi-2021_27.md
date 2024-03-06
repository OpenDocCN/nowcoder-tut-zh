# 第三章 第 20 节 深信服 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/b0d22f7331a64a92ae1af57c7392dba6`](https://www.nowcoder.com/tutorial/10028/b0d22f7331a64a92ae1af57c7392dba6)

# 深信服 2020 秋招笔试真题

![](img/1292f076b9a1300e80687b20b00aba94.png)

### 1、混淆报表

【题目描述】深信服项目经理在汇总一份报表，为避免部分敏感信息泄露，涉及到对报表数字的加密。

现在找到你，需要你来帮忙，实现一个算法，把报表内的数字进行加密：

对于指定的有符号整数 n，你需要将这个 n 中的数字反转来实现混淆。

**输入描述**

第一行输入一个数字

**输出描述**

输出反转后的数字

**备注**

假设硬件只能支持 32 位的有符号整数，则其数值范围为 [−2³¹,2³¹− 1]。基于这个前提，如果反转后整数溢出那么就返回 0。

**示例 1**

**输入**

```cpp
123
```

**输出**

```cpp
321
```

**示例 2**

**输入**

```cpp
-234
```

**输出**

```cpp
-432
```

**示例 3**

**输入**

```cpp
110
```

**输出**

```cpp
11
```

**【参考代码】**

```cpp
import sys
n = int(sys.stdin.readline())

def revert(x):
    if x >= 0:
        result = int(str(x)[::-1])
        if result - (2 ** 31 - 1) < 0:
            return result
        else:
            return 0
    else:
        result = -int(str(x)[-1:0:-1])
        if result > -2 ** 31 - 1:
            return result
        else:
            return 0

print(revert(n))
```

### 2、十进制数字

【题目描述】对于十进制数字，可以用普通的十进制表示法，比如 2020，也可以使用科学计数法，比如 2020e2020。

编写代码，验证指定的字符串是否可以解释为合法的十进制数字。

**输入描述**

第一行接收需要判断的字符串

**输出描述**

结果为真，则输出 1，否则输出 0

**备注**

在实现代码之前，你应当事先思考所有可能的情况。这里给出一份可能存在于有效十进制数字中的字符列表：
- 数字 0-9
- 指数  "e"
-  正/负号  "+"/"-"
- 小数点  "."

**示例 1**

**输入**

```cpp
0
```

**输出**

```cpp
1
```

**示例 2**

**输入**

```cpp
0.1
```

**输出**

```cpp
1
```

**示例 3**

**输入**

```cpp
abc
```

**输出**

```cpp
0
```

**示例 4**

**输入**

```cpp
-90e3
```

**输出**

```cpp
1
```

**【解题思路】**

直接由正则表达式匹配判断即可。

**【参考代码】**

```cpp
import sys
import re

s = sys.stdin.readline()

# code here
s = s.strip()
if bool(re.match(r'^[+-]?(\d+)?(\.\d+)(e[-+]?\d+)?$', s)
       &nbs***bsp;re.match(r'^[+-]?(\d+)(\.\d*)?(e[-+]?\d+)?$', s)):
    print(1)
else:
    print(0)
```

### 3、军训方阵

【题目描述】大学新生军训时，根据性别、班级、升高等将所有的新生分为若干个连队（每个连队的人数可能不一致），军训结束后统一阅兵时，希望将所有的连队排列成一个大致规整的矩形方阵，每一行的最大人数（MaxCount）可以手动指定。

要求：

1\. 连队的顺序不可改变。

2\. 所有的学生前后需要对齐。

3\. 每个连队的学生必须在同一行。（MaxCount 会大于等于 每一个连队的人数）。

4\. 同一连队的学生必须左右相邻，即之间不能有空位，而不同连队的学生之间可以有空位。

5\. 如果一行只有一个连队时，需要连队站在整行的中间位置，如无法在正中间，则需要左边的空位比右边的空位多一个。

6\. 如果一行有多个连队时，需要此行的最左边和最右边都站有人，连队之间可用空位分隔，要求尽可能均匀分配连队间的空位数量，如果某一行连队间的空位不能均匀分配，则左侧的空位数量要多于右侧的空位数量，且只能多一个。

**输入描述**

第一行依次输入每个连队的人数，以空格分隔
第二行输入最终方阵的每一行的最大人数

**输出描述**

最终方阵的展示信息。

**备注**

输出时，以+ 表示学生，以 - 表示空位。

**示例 1**

**输入**

```cpp
4 3 2 7 2 4 10 6 6 4 5 5 11
14
```

**输出**

```cpp
++++---+++--++
+++++++-++++++ 
--++++++++++--
++++++--++++++
++++++++++++++
--+++++++++++-
```

**说明**

输入：共 13 个连队，每个连队的人数如上。最终方阵每行的最大人数为 14
输出：
第一行：只能站 3 个连队，需要补充 5 个空位，左边的空位数量需要大于等于右边，且只能多一个
第二行：只能站 3 个连队，需要补充 1 个空位，左边的空位数量需要大于等于右边
第三行：只能站 1 个连队，需要补充 4 个空位，需要在整行的中间位置
第四行：只能站 2 个连队，需要补充 2 个空位，左右两边都需站人
第五行：刚好站 3 个连队，无需补充空位
第六行：只能站 1 个连队，需要补充 3 个空位，需要在整行的中间位置，左边的空位数量需要大于等于右边

**【参考代码】**

```cpp
# -*- coding: utf-8 -*-
import sys
import json

def print_out(out):
    p = []
    for i in out:
        if i == '-':
            p.append(i)
        else:
            p += '+' * i
    print(''.join(p))

def print_xx(numbers, max_count):
    empty_count = max_count - sum(numbers)

    if empty_count == 0:
        print_out(numbers)
        return

    if len(numbers) == 1:
        left = empty_count / 2
        if empty_count % 2:
            out = ['-'] * (left + 1)
        else:
            out = ['-'] * left
        out.append(numbers[0])
        out += ['-'] * left
        print_out(out)
        return

    t1 = empty_count / (len(numbers) - 1)
    t2 = empty_count % (len(numbers) - 1)

    out = []
    for item in numbers[0:-1]:
        out.append(item)
        if t2 > 0:
            out += ['-'] * (t1 + 1)
            t2 -= 1
        else:
            out += ['-'] * t1
    out.append(numbers[-1])
    print_out(out)

def do_it(numbers, max_count):
    tmp = []
    for item in numbers:
        if item > max_count:
            raise Exception('params err')

        if sum(tmp + [item]) <= max_count:
            tmp.append(item)
            continue

        print_xx(tmp, max_count)
        tmp = [item]

    print_xx(tmp, max_count)

if __name__ == '__main__':
    numbers = sys.stdin.readline().strip().split(' ')
    numbers = [int(d) for d in numbers]
    max_count = int(sys.stdin.readline())
    do_it(numbers, max_count)
```

### 4、第 N 长子串

【题目描述】对于指定的一个非空字符串数组，由若干个子串组成，通过算法实现：

查找并返回此数组中长度第 N 长的字符串长度，如果不存在，则返回字符串中最长子串的长度。

算法时间复杂度要求为 O(n)。

**输入描述**

第一行是字符串数组，由多个用空格隔开的字符串组成，字符只包含英文字母和数字，不区分大小写
第二行是整数 N (0<N<=100)

**输出描述**

输出一个整数，值为该子串的长度

**示例 1**

**输入**

```cpp
abc ab a
2
```

**输出**

```cpp
2
```

说明

第 2 长字符串为 "ab"，长度为 2

**示例 2**

**输入**

```cpp
abc ab ab
3
```

**输出**

```cpp
3
```

**说明**

ab 重复，算一个，第 3 长字符串不存在，最长字符串为 "abc"，长度为 3

**【解题思路】**

第 K 大问题，可以利用一个堆来维护当前最长 K 个子串的长度。

**【参考代码】**

```cpp
import sys
from heapq import nlargest
str_arr = sys.stdin.readline().strip().split(' ')
n = int(sys.stdin.readline().strip())
str_set = set(len(s) for s in str_arr)

if len(str_set) < n:
    n = 1

print nlargest(n, str_set)[-1]
```

### 5、团队搬家

【题目描述】深信服的云计算团队今年新招了很多新同学，原有办公室工位有限，就需要把团队进行搬迁。公司给每位同学都分了些箱子，用来打包个人物品，统一安排货车来运送，但安排多少车，才能保证最少成本保证最大运力，可就苦了负责搬迁事项的小姐姐。现在需要你的码力来帮小姐姐解决这个问题：

假设箱子都是统一大小，不同人会有不同数量的箱子，每辆货车的运力（可以运几个箱子）是一样的，设计个算法来计算需要安排几辆货车。

假设：

1<=人数<=50000

1<= 每个人的箱子数量<=货车运力<=30000

**输入描述:**

第一行输入个列表，列表元素表示每个人所拥有的箱子数量
第二行输入每辆货车的运力（可以运几个箱子）

**输出描述:**

输出需要安排的货车数量

**备注:**

额外要求：
1\. 每个人的箱子必须装在同一辆车
2\. 每辆车可以不装满
3\. 假设每辆车只跑一趟，保证一次运完

**示例 1**

**输入**

```cpp
1 2
3
```

**输出**

```cpp
1
```

 **说明**

1 辆车就可以把（1+2=3 个箱子运完

**示例 2**

**输入**

```cpp
3 2 2 1
3
```

**输出**

```cpp
3
```

**说明**

需要安排 3 辆车，(3),(2,1),(2)

**【解题思路】**

贪心，倒着尽量先把多的放了。

**【参考代码】**

```cpp
# -*- coding: utf-8 -*-
import sys

line1 = sys.stdin.readline().strip()
line2 = sys.stdin.readline().strip()

def calc(people, limit):
    people.sort(reverse=True)
    ans = 0
    ca = []
    for p in people:
        for i, c in enumerate(ca):
            if c + p <= limit:
                ca[i] = c + p
                if c + p == limit:
                    ans += 1
                    ca.pop(i)
                break
        else:
            ca.append(p)

    if ca:
        ans += len(ca)
    return ans

print calc(map(int, line1.split(' ')), int(line2))

```