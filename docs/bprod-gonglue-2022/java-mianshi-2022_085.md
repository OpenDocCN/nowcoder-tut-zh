# 第十章 第 11 节 NC7：股票（一次交易）

> 原文：[`www.nowcoder.com/tutorial/10070/cd3681adc27841df9d2ab2101e724ac6`](https://www.nowcoder.com/tutorial/10070/cd3681adc27841df9d2ab2101e724ac6)

### NC7：股票（一次交易）

**- 1、题目描述：**
![图片说明](img/b2bd317c84eeb35e6e22ce7e3af953e4.png "图片标题")

**- 2、题目链接：**

[`www.nowcoder.com/practice/64b4262d4e6d4f6181cd45446a5821ec?tpId=117&&tqId=34928&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking`](https://www.nowcoder.com/practice/64b4262d4e6d4f6181cd45446a5821ec?tpId=117&&tqId=34928&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking)
**-3、 设计思想：**
![ ](img/a17715b9d780e6c3d226078af422b125.png "图片标题")
![图片说明](img/95b7313313bd5e8920312deb3c6272e0.png "图片标题")
![图片说明](img/fe3b2168783069e9d328472b5322c146.png "图片标题")

**-5、代码：**
c++版本:

```cpp
 class Solution {
public:
    /**
     * 
     * @param prices int 整型 vector 
     * @return int 整型
     */
    int maxProfit(vector<int>& prices) {
        // write code here
        if(prices.size() == 0) return 0;
        int dp[prices.size()][2];
        dp[0][0] = 0;
        dp[0][1] =  prices[0];
        for(int i = 1;i < prices.size();i ++){
            dp[i][0] = max(dp[i-1][0],prices[i] - dp[i-1][1]);
            dp[i][1] = min(dp[i-1][1],prices[i]);
        }
        return dp[prices.size()-1][0];

    }
};
class Solution {
public:
    /**
     * 
     * @param prices int 整型 vector 
     * @return int 整型
     */
    int maxProfit(vector<int>& prices) {
        // write code here
        if(prices.size() == 0) return 0;
        int dp[2];
        dp[0] = 0;
        dp[1] = prices[0];
        for(int i = 1;i < prices.size();i ++){
            dp[0] = max(dp[0],prices[i] - dp[1]);
            dp[1] = min(dp[1],prices[i]);
        }
        return dp[0];

    }
};
class Solution {
public:
    /**
     * 
     * @param prices int 整型 vector 
     * @return int 整型
     */
    int maxProfit(vector<int>& prices) {
        // write code here
        if(prices.size() == 0)return 0; //如果给定数组的长度为 0，可以直接返回结果 0
        int min_input = prices[0];//初始化买入价格为第一个元素
        int max_output = 0;//定义一个最大的利润
        for(int i = 1;i < prices.size();i ++){
            min_input = min(min_input,prices[i]);//保存最小的买入价格
            max_output = max(max_output,prices[i]-min_input);//每遍历一个位置，就需要求出当前位置卖出的价钱
        }
        return max_output;

    }
};

```

Java 版本：

```cpp
import java.util.*;
public class Solution {
    /**
     * 
     * @param prices int 整型一维数组 
     * @return int 整型
     */
    public int maxProfit (int[] prices) {
        // write code here
        if(prices.length == 0) return 0;//如果给定数组的长度为 0，可以直接返回结果 0
        int min_input = prices[0];//初始化买入价格为第一个元素
        int max_output = 0;//定义一个最大的利润
        for(int i = 1;i < prices.length;i ++){
            min_input = Math.min(min_input,prices[i]);//保存最小的买入价格
            max_output = Math.max(max_output,prices[i] - min_input);//每遍历一个位置，就需要求出当前位置卖出的价钱
        }
        return max_output;

    }
}

```

Python 版本:

```cpp
#
# 
# @param prices int 整型一维数组 
# @return int 整型
#
class Solution:
    def maxProfit(self , prices ):
        # write code here
        if len(prices) == 0:#如果给定数组的长度为 0，可以直接返回结果 0
            return 0
        min_input = prices[0] #初始化买入价格为第一个元素
        max_output = 0#定义一个最大的利润
        for i in range(1,len(prices)):
            min_input = min(min_input,prices[i])#保存最小的买入价格
            max_output = max(max_output,prices[i] - min_input)#每遍历一个位置，就需要求出当前位置卖出的价钱
        return max_output

```