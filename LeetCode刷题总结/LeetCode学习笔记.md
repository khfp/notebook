<!--
 * @Author: your name
 * @Date: 2019-05-09 16:23:40
 * @LastEditTime: 2020-05-13 10:33:03
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \GitHubnotebook\LeetCode刷题总结\LeetCode学习笔记.md
 -->
# LeetCode学习笔记

前序和后序情况相反
中序首先需要定位到最左边的节点

## 1.二叉树前序遍历

    # Definition for a binary tree node.
    # class TreeNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.left = None
    #         self.right = None

递归：

    class Solution(object):
        def preorderTraversal(self, root):
            """
            :type root: TreeNode
            :rtype: List[int]
            """
            if not root:
                return []
            return [root.val] + self.preorderTraversal(root.left) + self.preorderTraversal(root.right)
迭代：

    class Solution:
        def preorderTraversal(self, root: TreeNode) -> List[int]:
            if not root:
                return []
            t = [root] # t是一个栈
            res = []
            while(t):
                node = t.pop()
                res.append(node.val)
                if node.right:
                    t.append(node.right)    # 先进右节点
                if node.left:
                    t.append(node.left)
            return res

## 2. 二叉树后序遍历

递归：

    class Solution:
        def postorderTraversal(self, root: TreeNode) -> List[int]:
            if not root:
                return []
            return self.postorderTraversal(root.left) + self.postorderTraversal(root.right) + [root.val]

迭代：

    class Solution:
        def postorderTraversal(self, root: TreeNode) -> List[int]:
            if not root:
                return []
            stack1, res = [], []
            stack1.append(root)
            while(stack1):
                cur=stack1.pop()
                # 接住每次弹出的节点
                res.insert(0,cur.val)
                if cur.left:
                    stack1.append(cur.left)
                if cur.right:
                    stack1.append(cur.right)
            return res

## 3. 二叉树中序遍历

递归：

    class Solution:
        def postorderTraversal(self, root: TreeNode) -> List[int]:
            if not root:
                return []
            return self.postorderTraversal(root.left) + [root.val] + self.postorderTraversal(root.right)

迭代：

    class Solution:
        def inorderTraversal(self, root: TreeNode) -> List[int]:
            if not root:
                return []
            stack, res = [], []
            cur = root
            while(cur or stack):
                if cur:
                    stack.append(cur)
                    cur = cur.left
                else:
                    cur = stack.pop()
                    res.append(cur.val)
                    cur = cur.right
            return res

# 二叉树层级遍历

迭代：

    class Solution:
        def levelOrder(self, root: TreeNode) -> List[List[int]]:
            if not root:
                return []
            queue,res = [root],[]
            while(queue):
                cur = queue.pop(0)
                res.append(cur.val)
                if cur.left:
                    queue.append(cur.left)
                if cur.right:
                    queue.append(cur.right)
            return res

## 重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

    # Definition for a binary tree node.
    # class TreeNode:
    #     def __init__(self, x):
    #         self.val = x
    #         self.left = None
    #         self.right = None

    class Solution:
        def __init__(self):
            self.dic = None
            self.pre = None
        def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
            self.pre = preorder
            self.dic = {}
            for i in range(len(inorder)):
                self.dic[inorder[i]] = i
            return self.rebuild(0,0,len(inorder)-1)
        
        def rebuild(self, pre_node, in_left, in_right):
            if in_left>in_right:
                return
            i = self.dic[self.pre[pre_node]]
            root = TreeNode(self.pre[pre_node])
            root.left = self.rebuild(pre_node+1,in_left,i-1)
            root.right = self.rebuild(pre_node+1+i-in_left,i+1,in_right)
            return root

## 位运算符

1. & 按位与运算符：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0
2. | 按位或运算符：只要对应的二个二进位有一个为1时，结果位就为1。
3. ^ 按位异或运算符：当两对应的二进位相异时，结果为1
4. ~ 按位取反运算符：对数据的每个二进制位取反,即把1变为0,把0变为1 
5. << 左移. >> 右移.


## 异或运算

### 1. 异或的性质

两个数字异或的结果a^b是将a和b的二进制每一位进行运算，得出的数字。 运算的逻辑是如果同一位的数字相同则为 0，不同则为 1。

### 2. 异或的规律

1. 任何数和本身异或都为0
2. 任何数和0异或都为本身
3. 异或运算满足交换律：a^b^c=a^c^b

