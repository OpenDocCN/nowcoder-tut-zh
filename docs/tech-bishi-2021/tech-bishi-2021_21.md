# 第三章 第 14 节 盛趣游戏 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/d8e5f250342b435783567e67d4ed2b83`](https://www.nowcoder.com/tutorial/10028/d8e5f250342b435783567e67d4ed2b83)

# 盛趣游戏 2020 秋招笔试真题

![](img/c548d3fa575b3c2489a25a63c69bba36.png)

### 1、最长公共部分

【题目描述】求出两个给定串的最长公共部分

**输入描述**

输入两行字符串

**输出描述**

输出公共部分

**示例 1**

**输入**

```cpp
11223344
112223344
```

**输出**

```cpp
223344
```

**【解题思路】**

经典动态规划问题。最长公共子串

**【参考代码】**

```cpp
#include <stdio.h>
#include <string.h>

int main() {
    char str[50000], patter[50000];
    scanf("%s", str);
    scanf("%s", patter);
    if (strlen(str) < strlen(patter)) {
        char temp[50000];
        strcpy(temp, str);
        strcpy(str, patter);
        strcpy(patter, temp);
    }
    int len = 0, maxlen = 0;
    char *res;
    for (int i = strlen(str) - 1; i >= strlen(patter); --i) {
        for (int k = strlen(patter) - 1; k >= 0; --k) {
            if (str[k + i - strlen(patter) + 1] == patter[k]) {
                len++;
            } else if (len > maxlen) {
                maxlen = len;
                res = &str[i + k - len];
                len = 0;
            } else
                len = 0;
        }
    }
    for (int i = 0; i < maxlen; ++i) {
        printf("%c", *(res + i));
    }
    printf("\n");
    return 0;
}
```

### 2、计算 bits 个数

【题目描述】给定一个 64bit 整数, 计算 bit 位为 1 的个数

**输入描述**

收入一个 64bit 的整数, 例如 3

**输出描述**

输出 2

**示例 1**

**输入**

```cpp
17
```

**输出**

```cpp
2
```

**【解题思路】**

位运算，拆分 64 位整数的每一位。

**【参考代码】**

```cpp
#include <iostream>
using namespace std;
int main() {
    long long int a;
    cin >> a;
    int count = 0;
    for (int i = 0; i < 64; i++) {
        if (a >> i & 1)
            count++;
    }
    cout << count;
}
```

### 3、购买玩具

【题目描述】现在有一笔钱, 要去采购玩具, 最多可以买多少种玩具

**输入描述**

输入一个玩具价格的数组和总钱数,例如: [1, 2, 3, 4, 5],10

**输出描述**

输出可以购买的玩具种类个数:4

**示例 1**

**输入**

```cpp
[1,2,3,4,5],10
```

**输出**

```cpp
4
```

**【解题思路】**

贪心，优先购买价格低的玩具。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    string vec;
    getline(cin, vec);
    int mey;
    cin >> mey;
    vector<int> arr;
    for (int i = 0; i < vec.size(); i++) {
        if (vec[i] == ' ')
            continue;
        int num = 0;
        if (vec[i] <= '9' && vec[i] >= '0') {
            while (vec[i] <= '9' && vec[i] >= '0') {
                num = num * 10 + vec[i] - '0';
                i++;
            }
            arr.push_back(num);
        }
    }

    sort(arr.begin(), arr.end());
    int count = 0;
    int j = 0;
    while (mey >= 0 && j < arr.size()) {
        mey = mey - arr[j];
        j++;
        if (mey >= 0) {
            count++;
        }
    }
    cout << count;
}
```

 ### 4、求和

【题目描述】给一个包含 n 个整数的数组 S, 找到与给定目标整数最接近的三个整数，并且返回这三个整数的和。

**输入描述**

第一个参数为给定目标整数后面跟随不定数量的整数数组

**输出描述**

输出与给定目标整数最接近的三个整数的和。

**示例 1**

**输入**

```cpp
3 2 7 11 15
```

**输出**

```cpp
20
```

**说明**

2+7+11=20

**示例 2**

**输入**

```cpp
1 -1 2 3 -4
```

**输出**

```cpp
4
```

**说明**

-1+2+3=4

**【解题思路】**

3-sum 问题，可以暴力枚举，也可以维护两个值的和再二分查找第三个值。

**【参考代码】**

```cpp
#include <bits/stdc++.h>
#define MAXN 100
int n[MAXN];

using namespace std;

void FindTarget(int target, int *n, int length) {
    int t = 999999;
    for (int i = 1; i < length; ++i) {
        for (int j = 2; j < length; ++j) {
            for (int k = 3; k < length; ++k) {
                int temp = abs(target - (n[i] + n[j] + n[k]));
                if (temp < t) {
                    t = n[i] + n[j] + n[k];
                }
            }
        }
    }

    printf("%d\n", t);
}
int main() {
    int i = 0;
    while (scanf("%d", &n[i]) != EOF) {
        ++i;
    }
    int target = n[0];
    FindTarget(target, n, i);
    return 0;
}
```

### 5、求交点

【题目描述】求给定射线同平面的交点。

**输入描述**

射线起点射线方向平面上的一点平面法线
RayPosX RayPosY RayPosZ RayDirX RayDirY RayDirZ PlanePosX PlanePosY PlanePosZ PlaneNormalX PlaneNormalY PlaneNormalZ

**输出描述**

返回精度为小数点后两位的交点坐标 x,y,z 字符串，数字之间用‘,’号分隔。

**示例 1**

**输入**

```cpp
155.8189 274.5527 58.25 0.2528733 0.003901914 0.9674916 155.5268 273.7294 63.13 0.1609947 0.05156531 -0.9856076
```

**输出**

```cpp
157.18,274.57,63.44
```

**示例 2**

**输入**

```cpp
155.8189 274.5527 58.25 -0.658873 0.2206937 0.7191527 
155.5268 273.7294 63.13 0.5249726 0.05127157 -0.8495736
```

**输出**

```cpp
152.79,275.57,61.55
```

**【解题思路】**

计算几何。求射线和平面的交点。

**【参考代码】**

```cpp
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
            double RayPosX = in.nextDouble();
            double RayPosY = in.nextDouble();
            double RayPosZ = in.nextDouble();
            double RayDirX = in.nextDouble();
            double RayDirY = in.nextDouble();
            double RayDirZ = in.nextDouble();
            double PlanePosX = in.nextDouble();
            double PlanePosY = in.nextDouble();
            double PlanePosZ = in.nextDouble();
            double PlaneNormalX = in.nextDouble();
            double PlaneNormalY = in.nextDouble();
            double PlaneNormalZ = in.nextDouble();
            //射线
            double np=PlanePosX*PlaneNormalX+PlanePosY*PlaneNormalY+PlanePosZ*PlaneNormalZ;
            double np0=PlaneNormalX*RayPosX+PlaneNormalY*RayPosY+PlaneNormalZ*RayPosZ;           
            double nd=PlaneNormalX*RayDirX+PlaneNormalY*RayDirY+PlaneNormalZ*RayDirZ;
            double t=(np-np0)/nd;

            double tx=t*RayDirX;
            double ty=t*RayDirY;
            double tz=t*RayDirZ;
            double fpx=RayPosX+tx;
            double fpy=RayPosY+ty;
            double fpz=RayPosZ+tz;
            fpx=(double)Math.round(fpx*100)/100;
            fpy=(double)Math.round(fpy*100)/100;
            fpz=(double)Math.round(fpz*100)/100;

            System.out.println(fpx+","+fpy+","+fpz);
    }
}

```