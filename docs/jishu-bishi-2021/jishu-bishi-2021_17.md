# 第三章 第 10 节 猿辅导 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/08df0f8dd3664a22aecf91be1af09998`](https://www.nowcoder.com/tutorial/10028/08df0f8dd3664a22aecf91be1af09998)

# 猿辅导 2020 秋招笔试真题

![](img/13082e1967831b0920cc31eb76115655.png)

### 1、小猿的迷宫之旅

【题目描述】有一个 N*M 大小的迷宫矩阵，迷宫的每一个格子有一个数值（a[i][j] <10⁹）。小猿在迷宫中发现，它只能朝着上下左右四个方向的相邻格子前进，并且只能进入比当前位置数值更大的格子。但是小猿有个紧急呼救按钮，他可以通过按下按钮，强行进入到不满足数值大小要求的相邻格子，可惜这个按钮只能按 K 次。请问小猿从这个迷宫任选一个格子出发，在紧急呼救按钮的帮助下，最多能走多少步（开始位置计入步数，即站在起点是步数为 1）。

**输入描述**

第一行输入三个数 N, M, K。接下来 N 行，每行 M 个数，表示迷宫中每个格子的值。
1≤N≤500
1≤M≤500
0≤K≤10

**输出描述**

输出小猿在迷宫中能走的最大步数

**示例 1**

**输入**

```cpp
3 3 1
1 3 3
2 4 9
8 9 2
```

**输出**

```cpp
6
```

**说明**

其中一种行走方案：(0, 0) -> (0, 1) -> (0, 0) -> (1, 0) -> (2, 0) -> (2, 1)

**【解题思路】**

Bfs 搜索迷宫问题。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int n, m, k;
const int maxn = 500 + 5, maxk = 10 + 1;
int a[maxn][maxn];
int mem[maxn][maxn];
int tmp[maxn][maxn];
struct state {
    int x, y;
    bool operator<(const state rhs) const {
        if (a[x][y] != a[rhs.x][rhs.y])
            return a[x][y] < a[rhs.x][rhs.y];
        if (x != rhs.x)
            return x < rhs.x;
        return y < rhs.y;
    }
} sts[maxn * maxn];
const int dx[] = {0, 1, 0, -1};
const int dy[] = {1, 0, -1, 0};
int bfs() {
    if (n == 1 && m == 1) {
        return 1;
    }
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            sts[i * m + j] = {i, j};
            mem[i][j] = 0;
        }
    }

    sort(sts, sts + n * m);
    for (int kk = 0; kk <= k; kk++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                tmp[i][j] = mem[i][j];
                for (int d = 0; d < 4; d++) {
                    const int tx = i + dx[d];
                    const int ty = j + dy[d];
                    if (tx >= 0 && tx < n && ty >= 0 && ty < m) {
                        tmp[i][j] = max(mem[tx][ty] + 1, tmp[i][j]);
                    }
                }
            }
        }

        memcpy(mem, tmp, sizeof(tmp));
        for (int t = 0; t < n * m; t++) {
            for (int d = 0; d < 4; d++) {
                const int tx = sts[t].x + dx[d];
                const int ty = sts[t].y + dy[d];
                if (tx >= 0 && tx < n && ty >= 0 && ty < m &&
                    a[tx][ty] > a[sts[t].x][sts[t].y]) {
                    mem[tx][ty] = max(mem[tx][ty], mem[sts[t].x][sts[t].y] + 1);
                }
            }
        }
    }

    int ret = 1;
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            ret = max(ret, mem[i][j]);
        }
    }
    return ret;
}
int main() {
    while (~scanf("%d%d%d", &n, &m, &k)) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                scanf("%d", a[i] + j);
            }
        }
        printf("%d\n", bfs());
    }
}
```

### 2、解压字符串

【题目描述】猿辅导 APP 需要下发一些宣传文本给学生，工程师们使用了一种字符压缩算法，为简单起见，假设被压缩的字符全部为大写字母序列，A,B,C,D....Z,压缩规则如下：

1.AAAB 可以压缩为 A3B (单字符压缩不加括号)

2.ABABA 可以压缩为(AB)2A （多字符串压缩才加括号）

输入数据保证不会出现冗余括号，且表示重复的数字一定合法且大于 1，即不会出现：

1.（A)2B   ------- （应为：A2B）

2.((AB))2C,-----(应为：（AB)2C)

3.（A)B  ----- （应为：AB）

4.A1B，（AB)1C，（应为 AB，ABC）

注意：数字可能出现多位数即 A11B 或者(AB)10C 或者 A02 这种情况。

A11B = AAAAAAAAAAAB

(AB)10C = ABABABABABABABABABABC

A02 = AA

数据分布：

对于 60%的数据，括号不会出现嵌套，即不会有 ((AB)2C)2 这种结构。

对于 80%的数据，括号最多只嵌套一层，即不会有 (((AB)2C)2D)99 这种结构。

对于 100%的数据，括号可以嵌套任意层。

**输入描述**

第一行是正整数 C(C<=100)，表示下面有 C 组数据。之后 C 行，每行为一组数据，每组数据为一个字符串。
每个字符串由 A-Z,数字 0-9 和(,)组成表示一个压缩后的串，保证输入数据一定合法且字符串长度小于 50。

**输出描述**

输出 C 行，每行对应一个数据的输出结果，表示压缩前的字符串，保证每个字符串展开后的长度不超过 10⁶。

**示例 1**

**输入**

```cpp
5
A11B
(AA)2A
((A2B)2)2G
(YUANFUDAO)2JIAYOU
A2BC4D2
```

**输出**

```cpp
AAAAAAAAAAAB
AAAAA
AABAABAABAABG
YUANFUDAOYUANFUDAOJIAYOU
AABCCCCDD
```

**【解题思路】**

关键是对括号的处理，由于要先处理最里层的括号，再处理最外层的括号，符合栈的性质。因此用一个栈来保存字符串开始重复的索引，也可以用搜索递归的性质解决。

**【参考代码】**

```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
using namespace std;

bool isdigit(char c) { return c >= '0' && c <= '9'; }

bool isalpha(char c) { return c >= 'A' && c <= 'Z'; }

int findRight(string str) {
    int num = 0;
    int len = str.length();
    for (int i = 0; i < len; ++i) {
        if (str[i] == '(') {
            num++;
        } else if (str[i] == ')') {
            num--;
        }
        if (str[i] == ')' && num == 0) {
            return i;
        }
    }
    return len - 1;
}

string getMultiStr(string str, int num) {
    string ans = "";
    while (num--) {
        ans += str;
    }
    return ans;
}

string dfs(string str) {
    int len = str.length();
    if (len == 0)
        return "";
    string temp = "";
    int num = 0;
    int index = 0;
    if (str[0] == '(') {
        int right = findRight(str);
        temp = dfs(str.substr(1, right - 1));
        index = right + 1;
        while (index < len) {
            if (isdigit(str[index])) {
                num = num * 10 + str[index] - '0';
            } else {
                break;
            }
            index++;
        }
        if (num == 0)
            num = 1;
        return getMultiStr(temp, num) + dfs(str.substr(index));
    } else {
        if (index + 1 < len && isdigit(str[index + 1])) {
            temp = str[index];
            index++;
            while (index < len) {
                if (isdigit(str[index])) {
                    num = num * 10 + str[index] - '0';
                } else {
                    break;
                }
                index++;
            }
            return getMultiStr(temp, num) + dfs(str.substr(index));
        } else {
            return str[0] + dfs(str.substr(1));
        }
    }
}

int main() {
    int C;
    string str;
    cin >> C;
    while (C--) {
        cin >> str;
        cout << dfs(str) << endl;
    }
    return 0;
}
```

### 3、分组对话

【题目描述】猿辅导课堂上老师提供了一些角色，学生可以从中选择一个自己喜欢的角色扮演，每 3 个不同的角色就可以组成一个小组，进行分组对话。

当老师点击开始分组对话按钮的时候，服务器会为已经选择自己角色的同学分配对话小组，请问最多能组成多少个对话小组？

**输入描述**

·第一行为测试用例数量 C(C<=100)，接下来的 C 行每行为一个测试用例
·每个用例的第一个数字表示可供选择的角色数量 T(T<=1000)，接下来的 T 个数字表示每个角色的选择人数 Pi

**输出描述**

一共 C 行，每行表示一个测试用例中的最大对话小组数量。

**示例 1**

**输入**

```cpp
3
3 1 1 1 
3 2 2 3
4 0 2 3 99
```

**输出**

```cpp
1
2
2
```

**说明**

·对于用例 1，正好 3 个不同角色，每个角色 1 个人选，于是构成且只能构成一个小组。
·对于用例 2，在构成两个小组之后，第 3 个角色单了 1 人无法构成任何小组，所以最大小组数量是 2。
·对于用例 3，学生扎堆选择了最后一个角色，但是第二个角色只有 2 个人，所以还是只能构成 2 个对话小组。

**【解题思路】**

用一个堆来模拟过程。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int n;
int main() {
    int c;
    cin >> c;
    while (c--) {
        cin >> n;
        priority_queue<int> q;
        while (n--) {
            int tmp;
            cin >> tmp;
            if (tmp)
                q.push(tmp);
        }
        int ret = 0;
        while (q.size() > 2) {
            int a = q.top();
            q.pop();
            int b = q.top();
            q.pop();
            int c = q.top();
            q.pop();
            ret += 1;
            if (a > 1)
                q.push(a - 1);
            if (b > 1)
                q.push(b - 1);
            if (c > 1)
                q.push(c - 1);
        }
        cout << ret << endl;
    }
}
```

### 4、课程质量建设

【题目描述】辅导课堂在推进质量建设，需要分析每堂直播课的用户报障数量。

当连续多个课程的报障数量之和大于一个数 s 的时候，系统会发出报警。小猿想知道最长连续的没有触发报警的课程数量。

**输入描述**

第一行两个整数 n，s
第二行 n 个整数，每个整数表示一次课程报障数量 a[i]
对于 10% 的数据，满足 1≤n≤2*10³
对于 100% 的数据，满足 1≤n≤2*10⁶，0≤a[i]≤10²

**输出描述**

最长连续的没有触发报警的课程数量

**示例 1**

**输入**

```cpp
3 2
1 1 3
```

**输出**

```cpp
2
```

**说明**

前面两次课程分别为 1，1 没有触发报警 所以答案是 2

**示例 2**

**输入**

```cpp
6 5
5 1 1 1 2 3
```

**输出**

```cpp
4
```

**说明**

中间的课程打分 1 1 1 2 之和等于 5 没有触发报警

**【解题思路】**

挨着扫一遍即可，同时维护几个信息进行判断。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n, S;
    cin >> n >> S;
    vector<int> scores;
    for (int i = 0; i < n; i++) {
        int j;
        cin >> j;
        scores.emplace_back(j);
    }
    int lengthMax = 0;
    for (int i = 0, j = 0, sum = 0; i < n; i++) {
        while (j < n && sum + scores[j] <= S) {
            sum += scores[j++];
        }
        if (j - i > lengthMax) {
            lengthMax = j - i;
        }
        sum -= scores[i];
    }
    cout << lengthMax << endl;
```

### 5、小猿的冠军班级

【题目描述】猿辅导课程中需要记录各个班的同学们的出勤情况并进行班级排名，授予冠军班级的奖励。

但是今天粗心的小猿出公司门的时候摔了一跤，把榜单给弄丢了，幸好考勤记录还没丢，但是顺序被弄乱了，现在他把考勤记录和班级名册整理了一下，请你写个程序，帮他把班级排名恢复吧！

排名规则是各班的出勤率，即老师在教室时同学们在教室听讲的比例，具体为：班级同学有效出勤分钟数之和/(老师在教室时间*班级人数)，出勤率相同的班级，按班级名称的字典序进行排序。

其中，有效出勤分钟数表示该同学与老师同在教室内的时间和，即各个区间的结束时间(分)与开始时间(分)之差的和。

**输入描述**

第一行为两个数字 N，M，以空格分隔，分别表示总考勤记录数和班级个数。
接下来 M 行，每行表示一个班级的情况，其中第 i+1 行数据为：
数字 K[i]表示该班级人数，数字 t[i]表示该班老师的用户 id，name[i]表示班级的名称，接下来 K[i]个数字表示该班的同学的用户 id。
例如：3 999 yuanxiaoyiban 0001 0002 0004
表示 yuanxiaoyiban 班的老师 id 为 999，3 位同学的用户 id 分别为 0001，0002，0004
接下来 N 行表示乱序的考勤记录，每一行表示一条记录，记录由命令 cmd[j]表示进出教室情况，有 IN 和 OUT 两种，数字 id[j]表示进出教室的用户 id，time[j]表示该记录发生的时间距 2000 年 1 月 1 日的分钟数。
例如：IN 999 1 表示 id 为 999 的用户在 2000 年 1 月 1 日 00:01 进入了教室。

数据保证，

所有人开始和结束记录时都不在教室内；

每个班级的老师在教室时间和班级人数不为 0；同一个用户在同一分钟可以进出教室各班级名称各不**一**次；相同。

**输出描述**

共 M 行，第 i 行为排名为 i 的班级的名称。

**备注**

数据范围：

0<N<300000,

0<M<10000,

所有班级的人数 K[i]总计小于 20000,

所有班级名称 name[i]的总长度小于 1000000，

且都由小写字母 a-z 组成。

0<id<2x10⁹,

记录时间 0<time[j] <1x10⁵

提示：由于笔试时间较为紧张，本题调试难度较大，建议动手前仔细阅读样例。

**示例 1**

**输入**

```cpp
12 2
3 999 yuanxiaoyiban 0001 0002 0004
2 9988 yuanxiaoerban 0003 0009
IN 0001 9001
OUT 0001 9006
IN 999 8888
OUT 999 8888
IN 999 9003
OUT 999 9004
IN 9988 9005
OUT 9988 9006
IN 0003 9001
OUT 0003 9002
IN 0003 9005
OUT 0003 9006
```

**输出**

```cpp
yuanxiaoerban
yuanxiaoyiban
```

**说明**

yuanxiaoyiban 出勤率为 1/3，yuanxiaoerban 出勤率为 1/2，因此 yuanxiaoerban 比 yuanxiaoyiban 出勤率高

**【解题思路】**

大模拟题，注意细节。

**【参考代码】**

```cpp
import sys
if __name__ == "__main__":
    line = sys.stdin.readline().strip()
    [n, m] = list(map(int, line.split()))
    courses = dict()
    students = dict()
    events = dict()
    for i in range(m):
        line = sys.stdin.readline().strip().split()
        teacher = int(line[1]);
        courses[teacher] = {"name":line[2],
                            "count":int(line[0]),
                            "students":dict(),
                            "in":0,
                            "teacherDuration":0,
                            "studentDuration":0}
        for j in line[3:]:
            students[int(j)] = teacher
    for i in range(n):
        line = sys.stdin.readline().strip().split()
        no = int(line[1])
        isIn = (line[0] == "IN")
        time = int(line[2])
        if events.get(time) == None:
            events[time] = {no:isIn}
        elif events[time].get(no) == None:
            events[time][no] = isIn
        else:
            events[time].pop(no)
    eventslist = list()
    for time,event in events.items():
        for no,isIn in event.items():
            eventslist.append((time, no, isIn))
    eventslist.sort()
    for event in eventslist:
        time = event[0]
        no = event[1]
        isIn = event[2]
        if courses.get(no) == None:
             course = courses[students.get(no)]
             if isIn:
                 if course["students"].get(no) != None and course["students"][no]["in"] != 0 and course["in"] != 0:
                    course["studentDuration"] = course["studentDuration"] + course["students"][no]["out"] - course["students"][no]["in"]
                 course["students"][no] = {"in":time, "out":0}
             else:
                 course["students"][no]["out"] = time
        else:
            course = courses[no];
            if isIn:
                course["in"] = time
            else:
                course["teacherDuration"] = time - course["in"] + course["teacherDuration"]
                course["in"] = 0
            for sn,student in course["students"].items():
                if isIn:
                    if student["in"] != 0 and student["out"] == 0:
                        student["in"] = time
                    else:
                        student["in"] = 0
                        student["out"] = 0
                else:
                    if student["in"] != 0:
                       if student["out"] == 0:
                            course["studentDuration"] = course["studentDuration"] + time - student["in"]
                       elif student["out"] > student["in"]:
                            course["studentDuration"] = course["studentDuration"] + student["out"] - student["in"]
    scores = list()
    for teacher, course in courses.items():
         scores.append((1.0 - float(course["studentDuration"]) / (course["count"] * course["teacherDuration"]), course["name"]))
    scores.sort()
    for score in scores:
        print(score[1])

```