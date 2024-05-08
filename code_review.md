
# 二叉树
---
## 二叉树的前序遍历->深度优先搜索：

**深度优先搜索**

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
            node = stack.pop()
            node = node.right  
        return res
``

---
## 二叉树的最大深度->广度优先遍历
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
