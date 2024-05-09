
# 二叉树: dfs, bfs, 递归

**需要复习的题目：236（有没有办法用dfs完成）**
---
### 深度优先搜索版本一：二叉树的前序遍历：

？思想与回溯类似？

``
    class Solution:
        def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
            res = []
            if not root:
                return []
            stack = []
            node = root
            while stack or node:
                while node:
                    res.append(node.val)
                    stack.append(node)
                    node = node.left
                node = stack.pop() #这一步应用到别的问题上容易出bug
                node = node.right  
            return res
``

### 深度优先搜索版本二：：
使用**内置函数**来替代进栈退栈的操作，例：求根节点到叶节点数字之和
``
    class Solution:
        def sumNumbers(self, root: Optional[TreeNode]) -> int:
            self.res = 0
            
            def dfs(root, preval):
                if not root:
                    return 
                preval = preval*10 + root.val
                if not root.left and not root.right:
                    self.res += preval
                    return
                dfs(root.left, preval)
                dfs(root.right, preval)
                return
    
            dfs(root, 0)
            return self.res
``

---
## 二叉树的最大深度->广度优先遍历

python中的list是天然的栈：即满足先进后出. **记得给nxt清零**
``
    class Solution:
        def maxDepth(self, root: Optional[TreeNode]) -> int:
            if not root: return 0
            deque = [root]
            nxt = []
            depth = 0
            while deque:
                node = deque.pop()
                if node.left: nxt.append(node.left)
                if node.right: nxt.append(node.right)
                if not deque:
                    depth += 1
                    deque = nxt
                    nxt = []
            return depth
``

---
## 广度优先搜索->路径总和

``
    class Solution:
        def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
            from collections import deque
            if not root:
                return False
            val = deque([root.val])
            mydeque = deque([root])
            while mydeque:
                node = mydeque.pop()
                nodeVal = val.pop()
                if node.left:
                    mydeque.append(node.left)
                    val.append(nodeVal+node.left.val)
                if node.right:
                    mydeque.append(node.right)
                    val.append(nodeVal+node.right.val)
                if not node.left and not node.right:
                    if nodeVal==targetSum:
                        return True
            return False
``

---
## 二叉搜索树->二叉搜索树中第k小的元素

二叉查找树（英语：Binary Search Tree），也称为二叉搜索树、有序二叉树（ordered binary tree）或排序二叉树（sorted binary tree），是指一棵空树或者具有下列性质的二叉树：

- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

**二叉搜索树的元素排序（从小到大）是广度优先搜索pop出来的顺序**
``
    class Solution:
        def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
            stack = []
            node = root
            flag = 0
            while node or stack:
                while node:
                    stack.append(node)
                    node = node.left
                node = stack.pop()
                flag += 1
                if flag==k:
                    return node.val
                node = node.right
``
