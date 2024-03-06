# 第三章 第 19 节 4399 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/35556e6c7a27478ab13a3529fad86035`](https://www.nowcoder.com/tutorial/10028/35556e6c7a27478ab13a3529fad86035)

# 4399 2020 秋招笔试真题

![](img/05f6585ec1cfcf7c9928c88552caa9da.png)

### 1、判断子网

【题目描述】请写一个脚本快速判断子网 192.168.1.1/16 内有哪些开放 80 端口的 ip？

**【解题思路】**

根据题意实现脚本。

**【参考代码】**

```cpp
#!/bin/bash
for mask in `seq 1 255`
do
for ip in `seq 1 255`
do
nc -zw 1 192.168.${mask}.${ip} 80 > /dev/null 2 >&1
if [$? -eq 0]
then
echo 192.168.${mask}.${ip} UP
else
echo 192.168.${mask}.${ip} DOWN
fi
done
done
```

### 2、实现 sortStr 方法

【题目描述】请编写 sortStr 方法，传入随机字符串，返回指定格式数组。

具体要求：

如输入 aaadd,则返回[[‘a', 3], [‘d': 2]];

如输入 bbcccaaaa，则返回[[a: 4], [c: 3], [b: 2]]。（输入的字符串仅限小写字母组成，且可忽略不同字母数量相同的情况）

**【解题思路】**

统计每个字符的个数，然后排列顺序即可。

**【参考代码】**

```cpp
function sortStr(str) {
    var strArr = str.split(""); // 字符串数组
    var chatArr = []; // 结果数组
    var charObj = {}; // 字符次数对象

    // 统计各字符出现次数  { a: 1, b: 2 }
    for(var i = 0; i < strArr.length; i++) {
     var charKey = strArr[i];
     if (charObj[charKey]) {
      charObj[charKey]++;
     }
     else {
      charObj[charKey] = 1;
     }
    }
    // 排列顺序
    for(var k in charObj) {
     var insertIndex = 0;
     chatArr.forEach((val, index) => {
      if (charObj[k] < val[1] ) {
       insertIndex = index + 1; // 添加的的位置
      }
     })
     // 添加到指定位置
     chatArr.splice(insertIndex, 0, [k, charObj[k]]);
    }

    return chatArr;
   }
}
```

### 3、双色球

【题目描述】从 1 ~ 33 随机抽取 6 个数打印出来（即 6 个红球），不能重复。从 1 ~ 16 随机抽取 1 个数打印出来（即 1 个蓝球）

已知随机函数为：int rand(int bound); // 输入 bound，随机返回一个范围为 [0, bound) 的整数

**【参考代码】**

```cpp
法 1：用列表保存 1~33，随机抽一个后，从列表里删掉
void printBall_1() {
    List<Integer> list = new ArrayList<Integer>();
    for(int i = 1; i <= 33; i++) {
        list.add(i);
    }

    // 打印 6 个红球，不重复
    for(int i = 0; i < 6; i++) {
        int idx = rand(list.size()); // 随机列表长度
        int red = list.get(idx);
        System.out.println(red);
        list.remove(idx);
    }

    // 打印 1 个蓝球，直接随机即可
    int blue = rand(16) + 1; // rand 值可能是 0，因此要加 1
    System.out.println(blue);
}

法 2：用数组保存 1~33，随机抽一个后，设为-1 标记已抽过，再抽到就重新抽
void printBall_2() {
    int red[] = new int[33];
    for(int i = 0; i < red.length; i++) {
        red[i] = i + 1;
    }
    // 打印 6 个红球，不重复
    int cnt = 0;
    while(cnt < 6) {
        int idx = rand(red.length);
        if(red[idx] > 0) {
            int pick = red[idx];
            System.out.println(pick);
            red[idx] = -1;
            cnt++;
        }
    }

    // 打印 1 个蓝球，直接随机即可
    int blue = rand(16) + 1; // rand 值可能是 0，因此要加 1
    System.out.println(blue);
}

```