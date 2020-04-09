<!--
 * @Author: your name
 * @Date: 2019-05-09 16:23:40
 * @LastEditTime: 2020-04-09 13:53:14
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
