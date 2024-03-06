# 第六章 第 1 节 NC72 二叉树的镜像

> 原文：[`www.nowcoder.com/tutorial/10069/2a967d85b8fb41c7a10b9874489a52ab`](https://www.nowcoder.com/tutorial/10069/2a967d85b8fb41c7a10b9874489a52ab)

### NC72 二叉树的镜像

**- 题目描述：**
![图片说明](img/f562dfbbf45df0182b27298444596fea.png "图片标题")

**- 题目链接：**
[`www.nowcoder.com/practice/564f4c26aa584921bc75623e48ca3011?tpId=117&&tqId=34994&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking`](https://www.nowcoder.com/practice/564f4c26aa584921bc75623e48ca3011?tpId=117&&tqId=34994&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking)

**- 设计思想：**
![图片说明](img/a5fbf90bf3e2fdb42ff1a0238711543a.png "图片标题")
详细操作流程看下图
![图片说明](img/0b57c211545221216d98793b22fcc504.png "图片标题")

**- 代码：**
c++版本:

```cpp
 /*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};*/
class Solution {
public:
    void Mirror(TreeNode *pRoot) {
        if(pRoot){//如果节点不为空就开始进行翻转
            //以下三行类似两个数交换
            TreeNode *temp = pRoot->left;//temp 用于临时存储 left 节点，以防止在复制的时候丢失
            pRoot->left = pRoot->right;
            pRoot->right = temp;
            Mirror(pRoot->left); //把当前节点的左孩子进行翻转
            Mirror(pRoot->right); //把当前节点的右孩子进行翻转
        }
    }
};

```

Java 版本：

```cpp
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
public class Solution {
    public void Mirror(TreeNode root) {
        if(root != null){ //如果节点不为空就开始进行翻转
            //以下三行类似两个数交换
            TreeNode temp = root.left; //temp 用于临时存储 left 节点，以防止在复制的时候丢失
            root.left = root.right;
            root.right = temp;
            Mirror(root.left); //把当前节点的左孩子进行翻转
            Mirror(root.right); //把当前节点的右孩子进行翻转
        }

    }
}

```

Python 版本:

```cpp
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

#
# 
# @param pRoot TreeNode 类 
# @return void
#
class Solution:
    def Mirror(self , pRoot ):
        if  pRoot : #如果节点不为空就开始进行翻转
            #以下三行类似两个数交换
            temp = pRoot.left #temp 用于临时存储 left 节点，以防止在复制的时候丢失
            pRoot.left = pRoot.right
            pRoot.right = temp
            self.Mirror(pRoot.left) #把当前节点的左孩子进行翻转
            self.Mirror(pRoot.right)#把当前节点的右孩子进行翻转

```