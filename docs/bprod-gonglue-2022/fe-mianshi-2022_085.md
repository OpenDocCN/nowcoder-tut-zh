# 第八章 第 6 节 NC37:合并区间

> 原文：[`www.nowcoder.com/tutorial/10072/e767a581782b484d9aeec0e7595826da`](https://www.nowcoder.com/tutorial/10072/e767a581782b484d9aeec0e7595826da)

### NC37:合并区间

**- 1、题目描述：**

![图片说明](img/bdee330a02897a8bb64a680845b05bd8.png "图片标题")
**- 2、题目链接：**
[`www.nowcoder.com/practice/69f4e5b7ad284a478777cb2a17fb5e6a?tpId=117&&tqId=34958&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking`](https://www.nowcoder.com/practice/69f4e5b7ad284a478777cb2a17fb5e6a?tpId=117&&tqId=34958&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking)

**-3、 设计思想：**
![图片说明](img/181c2c321958667af99b99bd82e70364.png "图片标题")
详细操作流程看下图
![图片说明](img/4c04b9fdce67130669340ae42ddd99c4.png "图片标题")

**- 代码：**
c++版本:

```cpp
 /**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
class Solution {
public:
     //定义排序规则，按照区间的左端点排序
     static bool cmp(const Interval &a,const Interval &b){
        return (a.start<b.start); 
    }
    vector<Interval> merge(vector<Interval> &intervals) {
        sort(intervals.begin(),intervals.end(),cmp);//排序
         vector<Interval>res;//返回的结果数组
         int i = 0,n = intervals.size();
         int l,r;
         while(i < n){
              l = intervals[i].start;//用来存储当前区间的左端
              r = intervals[i].end; //用来存储当前区间的右端
              //合并区间
             while(i < n-1 && r >= intervals[i + 1].start){
                 i ++;
                 r = max(r,intervals[i].end);
             }
             //将当前合并完的区间进行插入
             res.push_back({l,r});
             i ++;
         }
         return res;
    }
};

```

Java 版本：

```cpp
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
import java.util.*;
public class Solution {
    public ArrayList<Interval> merge(ArrayList<Interval> intervals) {
        intervals.sort((a, b) -> (a.start - b.start));//按照区间的左端点排序
        ArrayList<Interval> res = new ArrayList<Interval>();
        int i = 0,n = intervals.size();
        int l,r;
        while(i < n){
             l = intervals.get(i).start;//用来存储当前区间的左端
             r = intervals.get(i).end;//用来存储当前区间的右端
              //合并区间
             while(i < n-1 && r >= intervals.get(i + 1).start){
                 i ++;
                 r = Math.max(r,intervals.get(i).end);
             }
             //将当前合并完的区间进行插入
             res.add(new Interval(l, r));
             i ++;
         }
         return res;

    }
}

```

Python 版本:

```cpp
# class Interval:
#     def __init__(self, a=0, b=0):
#         self.start = a
#         self.end = b

#
# 
# @param intervals Interval 类一维数组 
# @return Interval 类一维数组
#
class Solution:
    def merge(self , intervals ):
        # write code here
        intervals = sorted(intervals,key = lambda x : x.start) #按照区间的左端点排序
        res = [] #用来存储最终的结果
        i,n = 0,len(intervals)
        while i < n:
            l = intervals[i].start #用来存储当前区间的左端
            r = intervals[i].end #用来存储当前区间的右端
            #合并区间
            while i < n - 1 and r >= intervals[i + 1].start:
                i += 1
                r = max(r,intervals[i].end)
            #将当前合并完的区间进行插入
            res.append(Interval(l,r))
            i += 1
        return res

```