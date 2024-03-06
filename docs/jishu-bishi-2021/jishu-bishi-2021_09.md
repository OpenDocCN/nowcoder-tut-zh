# 第三章 第 2 节 字节跳动 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/6727d6ea35e04809898eb91bd033d84b`](https://www.nowcoder.com/tutorial/10028/6727d6ea35e04809898eb91bd033d84b)

# 字节跳动 2020 秋招笔试真题

**![](img/9d5590d14f5f4911ad5a06ef9bca9b57.png)** 

### 1、模型文件去重

【题目描述】

抖音上不同的用户类型我们有不同的用户模型文件。
我们有一个模型配置文件，里面有很多的不同的用户类型和他们对应的模型文件。我们需要找出每个模型对应的是哪些用户类型。

给定一行输入，格式是 a b
a 表示这个用户的用户类型，b 表示这个用户对应的模型文件。
请你输出每个模型文件对应的用户类型。
注意 1：每个模型文件可能对应多个用户类型，用户类型之间用空格作为切分。
注意 2: 如果有多个用户类型输出，用户类型之间的排序按照字母表排序。
注意 3: 如果有多个模型输出，模型输出的顺序按照模型文件在输入数据中顺序，即从上到下。
**输入描述**
输入第 1 行: 用户类型 N（表示有多少个 用户类型）
接下来的 N 行：用户类型 模型文件

**输出描述**
模型文件用户类型 1 用户类型 2

**示例 1**
**输入**

```cpp
1
abc 1.txt
```

**输出**

```cpp
1.txt abc
```

**【解题思路】**

用一个 map 做 hash，其中以 string 作为 key，字符串去重集合 set<string>作为 value。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<string> l;
map<string, set<string>> s;

int main() {
    int n;
    string a, b;
    cin >> n;
    for (; n--;) {
        cin >> a >> b;
        if (!s.count(b))
            l.push_back(b);
        s[b].insert(a);
    }
    for (auto b : l) {
        cout << b;
        for (auto a : s[b]) {
            cout << " " << a;
        }
        cout << endl;
    }
    return 0;
}
```

### 2、穿越沙漠的补给次数

【题目描述】旅行者穿越沙漠的过程中需要不断地消耗携带的饮用水，到达终点前会经过几个绿洲，每个绿洲均设有水分补给站可以为旅行者提供水分补给并收取一定的费用。

沿途共有 n 个补给站，每个补给站收取的费用都一样，但是提供的水量不尽相同。起点到终点的距离为 D 公里，postion[i]表示第 i 个补给站距离起点的距离，单位为公里，supply[i]表示第 i 个补给站可以提供的水量，单位为升。

假设旅行者在起点时携带了 W 升的水，每行走 1 公里需要消耗 1 升的水量，身上可携带的水量没有上限，且携带的水量多少不会对体能消耗产生影响，鉴于每次进补给站花费的钱都是一样多，期望用最少的补给次数到达终点，请帮忙计算最少的补给次数。

**输入描述**

第一行输入整数 D 和 W, D 表示起点到终点的距离，W 表示初始携带的水量

第二行输入数组 postion，长度为 N，分别表示 N 个补给站分别距离起点的距离

第三行输入数组 supply，长度为 N, 分别表示 N 个补给站分别可以供给的水量

数据范围：1 <= D, W<=10⁸, 0<=N<=1000, 0<position[i],supply[i]<D

**输出描述**

输出一个整数表示最少的补给次数，若无法到达终点则返回-1

**示例 1**

**输入**

```cpp
10 4
1 4 7
6 3 5
```

**输出**

```cpp
1
```

**说明**

每行输入用空格隔开。起点到终点共 10 公里，初始时携带 4 升水，途径 3 个补给站。共需补给一次：只需在第 1 个补给站补给一次获得 6 升水，即可走完全程

**【解题思路】**

贪心。假设你现在有携带的水量为 W，当前位置为 curPos，那么你携带的水能够支撑你走到 curPos+W 位置，走到该位置之后你再考虑去该位置（如果有）及之前的水站加水（可能在该位置之前有多个水站），此外，为了保证最少的加水次数，应该到能加到最多水的水站加水，而且加完水之后应该给这个水站加一个标记（used 数组），以后不能再到该水站取水。

**【参考代码】**

```cpp
public static int solve(int D, int W, int[] pos, int[] sup){
    int res = 0;

    // 用来指示在特定的水站有没有取过水，一个水站只能取一次水
    boolean[] used = new boolean[pos.length];

    // 旅行者现在所在的位置
    int curPos = 0;

    while (curPos<D){
        // 每次直接跳到能够着的最大位置，携带的水也会被喝光
        curPos+=W;
        W = 0;
        // 如果已经到达终点，则直接返回加了多少次水
        if(curPos>=D) return res;

        // 标记一下能获得最多水的水站在 pos 中的下标
        int maxIndex = -1;

        for(int i=0;i<pos.length;i++){
            // 当前还没到指定的水站，则不能从这些水站取水，直接 break
            if(pos[i]>curPos) break;
            // 如果还没从该水站取水，则会看在这里取水能否得到最大的水量
            if(!used[i] && sup[i]>W) {
                W = sup[i]; maxIndex = i;
            }
        }

        // 没水了，而且也没有水站可以取水，可能在路途上被渴死
        if(maxIndex==-1) return -1;

        used[maxIndex] = true;
        res++;
    }
    return res;
}
```

 ### 3、走迷宫

【题目描述】给定一个迷宫，找到最快从起点到达重点的路径所需要的步数。
假设迷宫如下，假定左上角坐标为(0,0)，右下角坐标为 (3,2)
1 0 -1 1
-2 0 -1 -3
2 2 0 0
-2 是迷宫的起点，坐标为(0,1)
-3 是迷宫的终点，坐标为(3,1)
-1 代表障碍物，不能行走
1 和 2 代表传送门，传送门由正整数标示，只会成对出现。站在传送门上，能仅用一步就传送到相同数字的另一个传送门的位置：1 只能传送到 1，2 只能传送到 2。站在传送门上也可以选择不传送。
从起点到终点有若干种走法，举例如下：
(0,1)->(1,1)->(1,2)->(2,2)->(3,2)->(3,1)，共花费 5 步
或者
(0,1)->(0,0)-传送>(3,0)->(3,1)，共花费 3 步
经检验 3 步是所需的最少步数，最后结果返回 3

**输入描述**
每一行输入都是用空格隔开的整数
第一行给出迷宫地图的长和宽，均为正整数
之后每一行的每一个数字，都代表迷宫的一格
-2 表示起点，-3 表示终点，-1 表示不可通过的障碍物，0 表示可通过的道路，大于 0 的正整数代表传送门，并且保证成对出现，在传送门上，可以仅用一步传送到另一个相同数字的传送门的位置。

**输出描述**
输出最少要多少步能够从起点走到终点。
输出-1 如果没有任何办法从起点走到终点。

**备注**
迷宫大小<=200*200

**示例 1**
**输入**

```cpp
4 3
1 0 -1 1
-2 0 -1 -3
2 2 0 0
```

**输出**

```cpp
3
```

**说明**
(0,1)->(0,0)-传送>(3,0)->(3,1) ，共花费 3 步

**【解题思路】**

BFS 经典类型题目，注意边界细节处理。

**【参考代码】**

```cpp
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        System.out.println(new Solution().solve(in));
    }

    public static class Solution {

        public static class State {
            public final Square square;
            public int step;

            public State(Square square, int step) {
                this.square = square;
                this.step = step;
            }

            @Override
            public int hashCode() {
                return square.hashCode();
            }

            @Override
            public boolean equals(Object obj) {
                if (obj instanceof State) {
                    return this.square.equals(((State) obj).square);
                }
                return false;
            }

            public void nextUnvisitedStates(Square[][] map, Set<State> toVisit, Set<State> visited, Map<Integer, WarpGate> warpGate) {
                if (square.x > 0) {
                    Square left = map[square.x - 1][square.y];
                    addUnvisitedStateToList(left, toVisit, visited);
                }

                if (square.x < map.length - 1) {
                    Square right = map[square.x + 1][square.y];
                    addUnvisitedStateToList(right, toVisit, visited);
                }

                if (square.y > 0) {
                    Square up = map[square.x][square.y - 1];
                    addUnvisitedStateToList(up, toVisit, visited);
                }

                if (square.y < map[0].length - 1) {
                    Square down = map[square.x][square.y + 1];
                    addUnvisitedStateToList(down, toVisit, visited);
                }

                WarpGate gate = warpGate.get(square.type);
                if (gate != null) {
                    addUnvisitedStateToList(gate.getOtherSide(square), toVisit, visited);
                }
            }

            private void addUnvisitedStateToList(Square s, Set<State> toVisit, Set<State> visited) {
                State resultState = new State(s, this.step + 1);
                if (s.type != Square.SQUARE_BLOCK && !visited.contains(resultState) && !toVisit.contains(resultState)) {
                    toVisit.add(resultState);
                }
            }
        }

        public static class Square {
            public final int x;
            public final int y;
            public final int type;

            public static final int SQUARE_START = -2;
            public static final int SQUARE_END = -3;
            public static final int SQUARE_PATH = 0;
            public static final int SQUARE_BLOCK = -1;

            public boolean isWarpGate() {
                return type > 0;
            }

            public Square(int x, int y, int type) {
                this.x = x;
                this.y = y;
                this.type = type;
            }

            @Override
            public int hashCode() {
                return Objects.hash(x, y);
            }
        }

        public static class WarpGate {
            public Square s1;
            public Square s2;

            public Square getOtherSide(Square s) {
                if (s.equals(s1)) {
                    return s2;
                } else if (s.equals(s2)) {
                    return s1;
                } else {
                    return null;
                }
            }

            public void buildWarpGate(Square s) {
                if (s1 == null) {
                    s1 = s;
                } else if (s2 == null) {
                    s2 = s;
                } else {
                    throw new RuntimeException("Too many gates!");
                }
            }
        }

        public int solve(Square start, Square end, Square[][] map, Map<Integer, WarpGate> warpGate) {
            Set<State> visited = new HashSet<>();
            Set<State> queue = new LinkedHashSet<>();
            queue.add(new State(start, 0));

            while(!queue.isEmpty()) {
                Iterator<State> currentIt = queue.iterator();
                State current = currentIt.next();
                currentIt.remove();
                if (current.square.equals(end)) {
                    return current.step;
                }
                visited.add(current);
                current.nextUnvisitedStates(map, queue, visited, warpGate);
            }
            return -1;
        }

        public int solve(Scanner input) {
            String[] dimension = input.nextLine().split(" ");
            int width = Integer.valueOf(dimension[0]);
            int height = Integer.valueOf(dimension[1]);
            Square[][] map = new Square[width][height];
            int y = 0;
            Square start = null;
            Square end = null;
            Map<Integer, WarpGate> warpGate = new HashMap<>();
            while (input.hasNextLine()) {
                String line = input.nextLine();
                String[] squares = line.split(" ");
                for (int x = 0; x < width; x++) {
                    map[x][y] = new Square(x, y, Integer.valueOf(squares[x]));
                    if (map[x][y].type == Square.SQUARE_START) {
                        start = map[x][y];
                    } else if (map[x][y].type == Square.SQUARE_END) {
                        end = map[x][y];
                    } else if (map[x][y].isWarpGate()) {
                        int gateNumber = map[x][y].type;
                        WarpGate wg = warpGate.get(gateNumber);
                        if (wg == null) {
                            wg = new WarpGate();
                            warpGate.put(gateNumber, wg);
                        }
                        wg.buildWarpGate(map[x][y]);
                    }
                }
                y++;
            }
            if (start == null || end == null) {
                throw new RuntimeException("No Start&nbs***bsp;End Found");
            }
            return solve(start, end, map, warpGate);
        }
    }
}
```

### 4、简单变换

【题目描述】春节在家的凯凯真的是太无聊了，他准备和他家的猫玩一个游戏。
凯凯在黑板上写下了两个长度相等的数列 a[1...n], b[1...n]。
现在他想让他的猫判断数列 a 能否通过一个操作变换成数列 b。
这个操作是：在数列 a 中选择一个区间 l-r，对这个区间所有的数字加上一个 k。
其中 1<=l<=r<=n, k>=0。
你可以帮帮可怜的小猫做出这个判断么？
**输入描述**
首先输入一个数字 t，表示有 t 组数据
每组数据的第一行为一个数字 n 表示数列的长度
接下来两行每行有 n 个数字，分别为数组 a 和数组 b

**输出描述**
对于每组数据输出 YES 或者 NO
表示数列 a 能否通过对应的操作变换成数列 b。

**备注**
t<=10
n<=100000

**示例 1**
**输入**

```cpp
2
6
3 7 1 4 1 2
3 7 3 6 3 2
5
1 1 1 1 1
1 2 1 3 1
```

**输出**

```cpp
YES
NO
```

**说明**
对于第一个样例可以对区间[1,4,1] 的每个数字加上 2，即可把数列 a 转换成数列 b
对于第二个样例没法做相应的操作

**【解题思路】**

维护出一个差分数组，然后对差分数组进行情况讨论即可。

**【参考代码】**

```cpp
#include <bits/stdc++.h>

using namespace std;
int t, n;
int a[110000], b[110000];
int main() {
    scanf("%d", &t);
    for (; t > 0; t--) {
        memset(a, 0, sizeof(a));
        memset(b, 0, sizeof(b));
        scanf("%d", &n);
        for (int i = 0; i < n; ++i)
            scanf("%d", &a[i]);
        for (int i = 0; i < n; ++i)
            scanf("%d", &b[i]);
        for (int i = 0; i < n; ++i)
            a[i] = b[i] - a[i];
        int l = -1, r = -1, k = 0, flag = 1;
        for (int i = 0; i < n; ++i)
            if (a[i] != 0) {
                if (l == -1)
                    l = i;
                if (r == -1 || r == i - 1)
                    r = i;
                else if (r != -1 && r != i - 1) {
                    cout << "NO" << endl;
                    flag = 0;
                    break;
                }
                if (a[i] < 0) {
                    cout << "NO" << endl;
                    flag = 0;
                    break;
                }
                if (a[i] > 0) {
                    if (k != 0 && k != a[i]) {
                        cout << "NO" << endl;
                        flag = 0;
                        break;
                    }
                    k = a[i];
                }
            }

        if (!flag)
            continue;
        cout << "YES" << endl;
    }
    return 0;
}
```

### 5、优惠券

【题目描述】你有 n 种无门槛优惠券，每种优惠券有一个面值 a[i]。当一件商品的售价≥a[i]时，你可以出示这种优惠券直接抵扣。抵扣后，优惠券不会被回收，可以继续使用。现在，你想要买 m 件商品，每件商品的售价是 b[i]，请问你最少需要花费多少钱？

**输入描述**

第一行两个正整数 n,m(1≤n,m≤10⁶)

第二行 n 个正整数 a[i] (0≤a[i]≤10⁶)，代表 n 种无门槛优惠券的面值 （不保证排序）
第三行 m 个正整数 b[i] (0≤b[i] ≤10⁶)，代表 m 件商品的价格 （不保证排序）

**输出描述**

输出合理使用优惠券后，购买上述 m 件商品最少需要的花费。

**示例 1**

**输入**

```cpp
3 4
50 100 200
99 199 200 300
```

**输出**

```cpp
248
```

**【解题思路】**

二分查找出当前最优的优惠券进行使用。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> a;

int main() {
    int n, m;
    scanf("%d%d", &n, &m);
    a.push_back(0);
    for (int i = 0; i < n; i++) {
        int x;
        scanf("%d", &x);
        a.push_back(x);
    }
    sort(a.begin(), a.end());
    long long ans = 0;
    while (m--) {
        int x;
        scanf("%d", &x);
        ans += x - *--upper_bound(a.begin(), a.end(), x);
    }
    printf("%lld\n", ans);
}
```