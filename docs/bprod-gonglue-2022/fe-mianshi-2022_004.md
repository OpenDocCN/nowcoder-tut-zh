# 第二章 第 1 节 前端算法-1

> 原文：[`www.nowcoder.com/tutorial/10072/86537d776df64eefac8fa0e7bd1353bb`](https://www.nowcoder.com/tutorial/10072/86537d776df64eefac8fa0e7bd1353bb)

#### 2.1 树 找到某节点的路径

**参考答案**：

查找某个节点的路径的方法通常有两种，一种是递归算法,另一种是非递归算法

定义树节点

```cpp
// 树节点定义
class TreeNode{
    constructor(value){
        this.value = value;
        this.left = null;
        this.right = null;
    }
}
```

构建树

```cpp
// 构建树
let root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);
```

**递归算法**

```cpp
// 递归中序遍历二叉树
function midOrder(root) { 
    if(!root || !(root instanceof TreeNode)){
        return;
    }
    // 递归访问左子树
    midOrder(root.left);
    console.log(root.value);
    // 递归访问右子树
    midOrder(root.right);
}
midOrder(root);
```

**非递归算法**

```cpp
// 非递归中序遍历二叉树
function midOrderN(root) { 
    let p = root; // p 为当前遍历的节点， 初始为根
    let arr = []; // arr 作为栈
    while(p || arr.length !== 0){
        if(p){
            // 遍历左子树
            arr.push(p);
            // 每遇到非空二叉树先向做走
            p = p.left;
        }else{
            // p 为空，出栈
            let node = arr.pop();
            // 访问该节点
            console.log(node.value);
            // 向右走一次
            p = node.right;
        }
    }
 }
 midOrderN(root)
```

#### 2.2 洗完牌抽 5 张判断是否为同花顺

**参考答案**：

题目：从扑克牌中随机抽 5 张牌，判断是不是一个顺子，即这 5 张牌是不是连续的。2-10 为数字本身，A 为 1，J 为 11，Q 为 12，K 为 13，而大小王可以看成任意数字。

思路一:

我们需要把扑克牌的背景抽象成计算机语言。不难想象，我们可以把 5 张牌看成由 5 个数字组成的数组。大小王是特殊的数字，我们不妨把它们都当成 0，这样和其他扑克牌代表的数字就不重复了。接下来我们来分析怎样判断 5 个数字是不是连续的。最直观的是，我们把数组排序。但值得注意的是，由于 0 可以当成任意数字，我们可以用 0 去补满数组中的空缺。也就是排序之后的数组不是连续的，即相邻的两个数字相隔若干个数字，但如果我们有足够的 0 可以补满这两个数字的空缺，这个数组实际上还是连续的。举个例子，数组排序之后为{0，1，3，4，5}。在 1 和 3 之间空缺了一个 2，刚好我们有一个 0，也就是我们可以它当成 2 去填补这个空缺。于是我们需要做三件事情：把数组排序，统计数组中 0 的个数，统计排序之后的数组相邻数字之间的空缺总数。如果空缺的总数小于或者等于 0 的个数，那么这个数组就是连续的；反之则不连续。最后，我们还需要注意的是，如果数组中的非 0 数字重复出现，则该数组不是连续的。换成扑克牌的描述方式，就是如果一副牌里含有对子，则不可能是顺子。

思路二:

1）确认 5 张牌中除了 0，其余数字没有重复的（可以用表统计的方法）;

2) 满足这样的逻辑：（max，min 分别代表 5 张牌中的除 0 以外的最大值最小值）

​ 如果没有 0，则 max-min=4，则为顺子，否则不是

​ 如果有一个 0，则 max-min=4 或者 3，则为顺子，否则不是

​ 如果有两个 0，则 max-min=4 或者 3 或者 2，则为顺子，否则不是最大值和最小值在 1）中就可以获得，这样就 不用排序了

#### 2.3 爬楼梯 编代码

**参考答案**：

题目描述：

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意**：**给定 *n* 是一个正整数。

方法分析：

这道题主要是要明白该爬楼梯的规律其实就是符合斐波那契数列（Fibonacci Sequence） 规律的，问题就迎刃而解了。为什么说它是斐波那契数列呢？我们可以这样来思考：当我们从第 n-1 阶楼梯爬到第 n 阶楼梯时，需要 1 步；当我们从第 n-2 阶楼梯爬到第 n 阶楼梯时，需要 2 步.也就是说 到达第 n 阶楼梯的方法数等于到达第 n-1 阶楼梯的方法数加上到达第 n-2 阶楼梯的方法数，其正好符合斐波那契通项。

代码实现：

1.  采用递归实现

```cpp
var climbStairs = function(n) {
    if(n == 1) return 1;
    if(n == 2) return 2;
    return climbStairs(n-1) + climbStairs(n-2);
};
```

递归是求解斐波那契数列最经典和最直接的方式，其简洁易懂；但是递归特别费时，在该题中使用会出现[超出时间限制]的错误提示。

2.  数组方式

```cpp
var climbStairs = function(n) {
    let result = [1,2];
    for (let i = 2; i < n; i++) {
        result.push(result[i-1] + result[i-2]);
    }
    return result[n-1];
};
```

数组方式大大的减少了运行时间，我们先预设好前两项，再得到结果，返回数组最后一项即可。

3.  ES6 的方式

```cpp
var climbStairs = function(n) {
    let a = b = 1;
    for (let i = 0; i < n; i++) {
        [a, b] = [b, a + b];
    }
    return a;
};
```

其中 [a, b] = [b, a + b] 表示解构赋值，其等价于

```cpp
temp = a;
a = b;
b = temp + b;
```

#### 2.4 怎么识别 100 枚硬币中的假币

**参考答案**：

**问题描述：**
在 n 枚外观相同的硬币中，有一枚是假币，并且已知假币与真币的重量不同，但不知道假币与真币相比较轻还是较重。可以通过一架天平来任意比较两组硬币，设计一个高效的算法来检测这枚假币(**以下提供两种方法**)。
**解题思路 1 (本例为真币重量大于假币)：**
使用减治法的解题思路，将硬币分为 3 堆，则每堆的硬币数量为 n/3 ，但是这是在 n%3==0 的情况下才能成立，所以我们将 n 枚硬币分为 3 堆加 1 堆 余数堆(余数堆可能为 0)，则可分为如下(n-n%3)/3, (n-n%3)/3, (n-n%3)/3, n%3。
**如下分组：**
a 堆： (n-n%3)/3
b 堆： (n-n%3)/3
c 堆： (n-n%3)/3
d(余数堆): n%3
**逻辑流程：**

1.  判断 n 中的硬币数量，如果 n>2 则执行 2，否则执行 5.
2.  将 n 分为上图的四堆，拿 a 和 b 比较，如果 a == b ,则 假币在 c 或 d 中。否则假币在 a 或 b 中。
3.  如果 a == b，则拿 a 和 c 比较。如果 a == c,则假币在 d(余数堆)中。将 d 再次 执行流程 1，并且 n=n%3。如果不等，则假币在 c 中，将 c 再次 执行流程 1，并且 n=(n-n%3)/3。
4.  如果 a != b，则拿 a 和 c 比较。如果 a == c,则假币在 b 中，将 b 再次 执行流程 1，并且 n=(n-n%3)/3。如果不等，则假币在 a 中，将 a 再次 执行流程 1，并且 n=(n-n%3)/3。
5.  如果 n==2，则将两枚硬币进行比较找出假币。
6.  如果 n==1，则该硬币就是假币，输出结果结束。

**解题思路 2（以 12 枚硬币为例，且假币未知轻重）：**

1.  将硬币编号：1，2，3，4，5，6，7，8，9，10，11，12。三次称重如下安排：
2.  称重：
    第一次称重：左盘：1，2，3，4 右盘：5，6，7，8 其他：9，10，11，12
    第二次称重：左盘：1，6，7，8 右盘：5，10，11，12 其他：9，2，3，4
    第三次称重：左盘：5，6，10，2 右盘：9，7，11，3 其他：1，8，12，4
    称重结果：平衡取 0，左倾取 1，右倾取-1。
    3 次称重安排可表示成矩阵形式：

![](img/b6ead15eb3f36623ebf1aa58c5738bcf.png)
其中，矩阵第一行是硬币序号，下面每一行都是一次称重结果，1 表示该硬币放左盘，-1 表示放右盘，0 表示不放。矩阵每一列为检测结果，检测结果对应的硬币序号为假币。如果结果与上边的符合，则对应重量为重，如果结果不包含在上述表中，则进行 1 -1 互换，得到的重量为轻。例如：若称重结果是 110，则 1 号为假币，且重量较重：若称重结果为 1-10，1 与-1 进行交换后为-110，则 8 号为假币，且重量较轻。

#### 2.5 手撕快排算法

**参考答案**：

思想：

1.  在数据集之中，选择一个元素作为"基准"（pivot）。
2.  所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。
3.  对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

实现：

```cpp
var quickSort = function(arr) {
　　if (arr.length <= 1) { return arr; }
　　var pivotIndex = Math.floor(arr.length / 2);
　　var pivot = arr.splice(pivotIndex, 1)[0];
　　var left = [];
　　var right = [];
　　for (var i = 0; i < arr.length; i++){
　　　　if (arr[i] < pivot) {
　　　　　　left.push(arr[i]);
　　　　} else {
　　　　　　right.push(arr[i]);
　　　　}
　　}
　　return quickSort(left).concat([pivot], quickSort(right));
};
```

#### 2.6 常见的排序算法和它们的时间复杂度是多少？

**参考答案**：

| 稳定的排序 | 时间复杂度 | 空间复杂度 |
| --- | --- | --- |
| 冒泡排序(bubble sort) | 最差、平均都是 O(n²)，最好是 O(n) | 1 |
| 插入排序(insertion sort) | 最差、平均都是 O(n²)，最好是 O(n) | 1 |
| 归并排序(merge sort) | 最差、平均、最好都是 O(n log n) | O(n) |
| 桶排序(bucket sort) | O(n) | O(k) |
| 基数排序(Radix sort) | O(nk)（k 是常数） | O(n) |
| 二叉树排序(Binary tree sort) | O(n log n) | O(n) |

| 不稳定的排序 | 时间复杂度 | 空间复杂度 |
| --- | --- | --- |
| 选择排序(selection sort) | 最差、平均都是 O(n²) | 1 |
| 希尔排序(shell sort) | O(n log n) | 1 |
| 堆排序(heapsort) | 最差、平均、最好都是 O(n log n) | 1 |
| 快速排序(quicksort) | 平均是 O(n log n)，最差是 O(n²) | O(log n) |

#### 2.7 说一下归并排序思想怎么实现的

**参考答案**：

“归并”的意思是将两个或两个以上的有序表组合成一个新的有序表。假如初始序列含有 n 个记录，则可看成是 n 个有序的子序列，每个子序列的长度为 1，然后两两归并，得到[n/2]（向上取整）个长度为 2 或 1 的有序子序列；再两两归并，……，如此重复，直到得到一个长度为 n 的有序序列为止，这种排序方法称为 2-路归并排序。

步骤解析：

1、把长度为 n 的输入序列分成两个长度为 n/2 的子序列；

2、对这两个子序列继续分为 m/2 的子序列，一直分下去，直为 1 个元素；

3、将两个排序好的子序列合并成一个最终的排序序列。

特点：

速度仅次于快速排序，为稳定排序算法，一般用于总体无序，但是各子项相对有序的数列，属于分治思想，递归归并。

动图演示：

![](img/fa5b0d0592d1f65338c7e32f21caabc1.png)

**JavsScript 代码实现：**

```cpp
//归并排序
function mergeSort(arr){
  var len = arr.length;
  if(len < 2){
    return arr;
  }

  //首先将无序数组划分为两个数组
  var mid = Math.floor(len / 2);
  var left = arr.slice(0,mid);
  var right = arr.slice(mid,len);
  return merge(mergeSort(left),mergeSort(right));//递归分别对左右两部分数组进行排序合并
}
//合并
function merge(left,right){
  var result = [];
  while(left.length>0 && right.length>0){
    if(left[0]<=right[0]){
      //如果左边的数据小于右边的数据，将左边数据取出，放在新数组中
      result.push(left.shift());
    }else{
       result.push(right.shift());
     }
  }

  while(left.length){
     result.push(left.shift());
  }
  while(right.length){
     result.push(right.shift());
  }
  return result;
}
var arr = [3,44,38,5,47,15,36,26];
console.log(mergeSort(arr));//3,5,15,26,36,38,44,47
```

#### 2.8 算法：3 数之和

**参考答案**：

题目描述：

给定一个包含 *n* 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？找出所有满足条件且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

```cpp
//例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
//满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
var threeSum = function(nums) {
    nums=nums.sort(function(a,b){return a-b});//先排序
    var i=0;
    var arr=[];
    while(i<nums.length-1){
        var a=nums[i],j=i+1,k=nums.length-1;
        while(j<k){
            var b=nums[j],c=nums[k];
            var sum=a+b+c;
            if(sum==0)arr.push([a,b,c]);//存起来
            if(sum<=0)
                while(nums[j]==b&&j<k)j++;//第 2 个
            if(sum>=0)
                while(nums[k]==c&&j<k)k--//最后一个数
        }
        while(nums[i]==a&&i<nums.length-1)i++;//第一个
    }
    return arr
};
```

#### 2.9 算法：连续最大乘积

**参考答案**：

题目描述

给一个浮点数序列，取最大乘积连续子串的值，例如 -2.5，4，0，3，0.5，8，-1，则取出的最大乘积连续子串为 3，0.5，8。也就是说，上述数组中，3 0.5 8 这 3 个数的乘积 3*0.5*8=12 是最大的，而且是连续的。

分析与解法

此最大乘积连续子串与最大乘积子序列不同，请勿混淆，前者子串要求连续，后者子序列不要求连续。也就是说，最长公共子串（Longest CommonSubstring）和最长公共子序列（LongestCommon Subsequence，LCS）是：

*   子串（Substring）是串的一个连续的部分，
*   子序列（Subsequence）则是从不改变序列的顺序，而从序列中去掉任意的元素而获得的新序列；

更简略地说，前者（子串）的字符的位置必须连续，后者（子序列 LCS）则不必。比如字符串“ acdfg ”同“ akdfc ”的最长公共子串为“ df ”，而它们的最长公共子序列 LCS 是“ adf ”，LCS 可以使用动态规划法解决。

解法一

或许，读者初看此题，可能立马会想到用最简单粗暴的方式：两个 for 循环直接轮询。

```cpp
double maxProductSubstring(double *a, int length)
{
    double maxResult = a[0];
    for (int i = 0; i < length; i++)
    {
        double x = 1;
        for (int j = i; j < length; j++)
        {
            x *= a[j];
            if (x > maxResult)
            {
                maxResult = x;
            }
        }
    }
    return maxResult;
}
```

但这种蛮力的方法的时间复杂度为 O(n²)，能否想办法降低时间复杂度呢？

解法二

考虑到乘积子序列中有正有负也还可能有 0，我们可以把问题简化成这样：数组中找一个子序列，使得它的乘积最大；同时找一个子序列，使得它的乘积最小（负数的情况）。因为虽然我们只要一个最大积，但由于负数的存在，我们同时找这两个乘积做起来反而方便。也就是说，不但记录最大乘积，也要记录最小乘积。

假设数组为 a[]，直接利用动态规划来求解，考虑到可能存在负数的情况，我们用 maxend 来表示以 a[i]结尾的最大连续子串的乘积值，用 minend 表示以 a[i]结尾的最小的子串的乘积值，那么状态转移方程为：

```cpp
  maxend = max(max(maxend * a[i], minend * a[i]), a[i]);
  minend = min(min(maxend * a[i], minend * a[i]), a[i]);
```

初始状态为 maxend = minend = a[0]。

参考代码如下：

```cpp
double MaxProductSubstring(double *a, int length)
{
    double maxEnd = a[0];
    double minEnd = a[0];
    double maxResult = a[0];
    for (int i = 1; i < length; ++i)
    {
        double end1 = maxEnd * a[i], end2 = minEnd * a[i];
        maxEnd = max(max(end1, end2), a[i]);
        minEnd = min(min(end1, end2), a[i]);
        maxResult = max(maxResult, maxEnd);
    }
    return maxResult;
}
```

动态规划求解的方法一个 for 循环搞定，所以时间复杂度为 O(n)。