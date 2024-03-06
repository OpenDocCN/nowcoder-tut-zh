# 第六章 第 17 节 NC33 合并有序链表

> 原文：[`www.nowcoder.com/tutorial/10069/097c214054ca4fd98e686b78b06badd1`](https://www.nowcoder.com/tutorial/10069/097c214054ca4fd98e686b78b06badd1)

### NC33 合并有序链表

**- 1、题目描述：**

![图片说明](img/496540649142f71f63ee63cd8b12057a.png "图片标题")
**- 2、题目链接：**
[`www.nowcoder.com/practice/a479a3f0c4554867b35356e0d57cf03d?tpId=117&&tqId=34954&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking`](https://www.nowcoder.com/practice/a479a3f0c4554867b35356e0d57cf03d?tpId=117&&tqId=34954&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking)

**-3、 设计思想：**
![图片说明](img/18407761b67ae08ebe7f25f30183ff70.png "图片标题")
详细操作流程看下图：
![图片说明](img/0dd6eba2a367cb8573e5fe82e3d32f81.png "图片标题")

**-5、代码：**
c++版本:

```cpp
 /**
 * struct ListNode {
 *    int val;
 *    struct ListNode *next;
 * };
 */

class Solution {
public:
    /**
     * 
     * @param l1 ListNode 类 
     * @param l2 ListNode 类 
     * @return ListNode 类
     */
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        // write code here
        if(l1 == NULL) return l2; //如果 l1 为空返回 l2
        if(l2 == NULL) return l1;//如果 l2 为空返回 l1
        ListNode *res = new ListNode(-1);//创建 res 链表用于返回最终结果链表
        ListNode *pre = res;//pre 的作用是进行链表的合并
        while(l1 != NULL && l2 != NULL){
            if(l1->val < l2->val){ //如果 l1.val < l2.val 就让当前 l1 节点进入 res
                pre->next = l1;
                l1 = l1->next;
            }else{//如果 l1.val > l2.val 就让当前 l2 节点进入 res
                pre->next = l2;
                l2 = l2->next;
            }
            pre = pre->next;//pre 指针移动到下一个
        }
        pre->next = l1 == NULL?l2:l1;//将不为空的那个没有遍历完的链表部分直接插入
        return res->next;//因为头指针为-1 所以直接返回头指针的下一个节点
    }
};

```

Java 版本：

```cpp
import java.util.*;

/*
 * public class ListNode {
 *   int val;
 *   ListNode next = null;
 * }
 */

public class Solution {
    /**
     * 
     * @param l1 ListNode 类 
     * @param l2 ListNode 类 
     * @return ListNode 类
     */
    public ListNode mergeTwoLists (ListNode l1, ListNode l2) {
        // write code here
        if(l1 == null) return l2;//如果 l1 为空返回 l2
        if(l2 == null) return l1;//如果 l2 为空返回 l1
        ListNode res = new ListNode(-1);//创建 res 链表用于返回最终结果链表
        ListNode pre = res;//pre 的作用是进行链表的合并
        while(l1 != null && l2 != null){
            if(l1.val < l2.val){//如果 l1.val < l2.val 就让当前 l1 节点进入 res
                pre.next = l1;
                l1 = l1.next;
            }else{//如果 l1.val > l2.val 就让当前 l2 节点进入 res
                pre.next = l2;
                l2 = l2.next;  
            }
            pre = pre.next;//pre 指针移动到下一个
        }
        pre.next = l1 == null?l2:l1;//将不为空的那个没有遍历完的链表部分直接插入
        return res.next;//因为头指针为-1 所以直接返回头指针的下一个节点
    }
}

```

Python 版本:

```cpp
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

#
# 
# @param l1 ListNode 类 
# @param l2 ListNode 类 
# @return ListNode 类
#
class Solution:
    def mergeTwoLists(self , l1 , l2 ):
        # write code here
        if l1 == None: return l2#如果 l1 为空返回 l2
        if l2 == None: return l1#如果 l2 为空返回 l1
        res = ListNode(-1)#创建 res 链表用于返回最终结果链表
        pre = res#pre 的作用是进行链表的合并
        while l1 != None and l2 != None:
            if l1.val < l2.val:#如果 l1.val < l2.val 就让当前 l1 节点进入 res
                pre.next = l1
                l1 = l1.next
            else:#如果 l1.val > l2.val 就让当前 l2 节点进入 res
                pre.next = l2
                l2 = l2.next
            pre = pre.next#pre 指针移动到下一个
        if l1 == None:#将不为空的那个没有遍历完的链表部分直接插入
            pre.next = l2
        else:
            pre.next = l1
        return res.next#因为头指针为-1 所以直接返回头指针的下一个节点

```