
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
---
## 图

### 广度优先搜索->岛屿系列
``

    class Solution:
        def numIslands(self, grid: List[List[str]]) -> int:
            from collections import deque
            m, n = len(grid), len(grid[0])
            res = 0
            queue = deque([])
            for i in range(m):
                for j in range(n):
                    if grid[i][j]=='1':
                        queue.append([i,j])
                        grid[i][j] = '0' #将走过的‘1’改为0，避免使用数组visited
                        res += 1
                        while queue:
                            mx, my = queue.popleft()
                            for x, y in [mx-1, my], [mx+1, my], [mx, my+1], [mx, my-1]:
                                if 0<=x<m and 0<=y<n and grid[x][y]=='1':
                                    queue.append([x,y])
                                    grid[x][y] = '0'
            return res

``

---
## 回溯

### 广度优先搜索->全排列
**需要再刷的题目：79**
- res.append的时候，需要写成res.append(each[:])
- 不管visited是dict还是list，在dfs前后要进行相反的操作，比如在dfs之前visited[y] = True，退出dfs之后需要visited[y] = False
- 避免重复的一个方法是sort

``

    class Solution:
        def permute(self, nums: List[int]) -> List[List[int]]:
    
            def dfs(visited, each):
                if len(each)==n:
                    res.append(each[:])
                    return
                for y in nums:
                    if not visited.get(y):
                        visited[y] = True
                        each.append(y)
                        dfs(visited, each)
                        each.pop()
                        visited[y] = False
                return
            res = []
            n = len(nums)
            dfs({}, [])
            return res
``

---
## 动态规划

### 一维动态规划->打家劫舍

``

    class Solution:
        def rob(self, nums: List[int]) -> int:
            if len(nums)==1:
                return nums[0]
            elif len(nums)==2:
                return max(nums[0], nums[1])
            dp = [nums[0], max(nums[0], nums[1])]
            for i in range(2, len(nums)):
                dp.append(max(dp[i-2]+nums[i], dp[i-1]))
            return dp[-1]
``

### 二维动态规划->最长回文子串
二维动态规划的题目包括
- 数据本身是二维的，构建的dp数组也是二维的，这种类型很直观，其实和一维dp没什么区别
- 数据本身是一维的，但是dp数组是二维的，只要可以想到用二维dp，一般不会很困难，需要注意的地方是在用到dp[i][j]时，这个值已经被更新了

``

    class Solution:
        def longestPalindrome(self, s: str) -> str:
            n = len(s)
            dp = [[0]*n for _ in range(n)]
            for i in range(n):
                dp[i][i] = 1
            res = s[0]
            for j in range(n):
                for i in range(j):
                    if (dp[i+1][j-1]==1 or j-i==1) and s[i]==s[j]:
                        dp[i][j]=1
                        if j-i+1>len(res): res = s[i:j+1]
            return res
``

---
## 二分查找

### 寻找旋转排序数组的最小值

``

    class Solution:
        def findMin(self, nums: List[int]) -> int:
            n = len(nums)
            left, right = 0, n-1
            while left<=right:
                mid = int((left+right)/2)
                if nums[mid]<nums[right]:
                    right = mid
                else:
                    left = mid+1
            return nums[mid]

``
