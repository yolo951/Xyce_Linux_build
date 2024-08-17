
# 数组

**需要复习的题目：80, 189, 122, 55, 45, 380, 134, 42, 68**


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

**需要再刷的题目：97, 72, 221**

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

### 二维动态规划->交错字符串

``

    class Solution:
        def isInterleave(self, s1: str, s2: str, s3: str) -> bool:
            if not len(s1)+len(s2)==len(s3):
                return False
            n1 = len(s1)
            n2 = len(s2)
            dp = [[0]*(n2+1) for _ in range(n1+1)]
            # dp的两个维度分别为n1+1, n2+1, 这样就可以避免考虑s1=''或者s2=''的情况
            # dp[i, j]=1: s1[:i]和s2[:j]可以交错得到s3[:i+j+1]
            # 不需要在子问题里面考虑是否交错
            dp[0][0] = 1
            for i in range(n1+1):
                for j in range(n2+1):
                    if (i>=1 and dp[i-1][j] and s1[i-1]==s3[i+j-1]) or (j>=1 and dp[i][j-1] and s2[j-1]==s3[i+j-1]):
                        dp[i][j] = 1
            return True if dp[-1][-1] else False

``

---
## 二分查找
**需要再刷的题目：旋转排序数组类(33，153), 162**
### 寻找旋转排序数组的最小值

``

    class Solution:
        def findMin(self, nums: List[int]) -> int:
            n = len(nums)
            if n==1: return nums[0]
            left, right = 0, n-1
            while left<right:  # 写成'<='容易进入死循环, 如果更新right为right = mid-1，就不会出现死循环，但是这个题不可以这样更新.另外当len(nums)=1时，不会进入循环，注意报错
                mid = int((left+right)/2)
                if mid+1<n and nums[mid]>nums[mid+1]: return nums[mid+1]
                if nums[mid]<nums[right]:  # 说明转折点在[left,mid]
                    right = mid
                else:  # 说明转折点在[mid+1,right]
                    left = mid+1
            return nums[mid]

``

---

## 哈希表
**需要再刷的题目：128（最长连续序列）**


---

## 链表
**需要再做一遍的题目：146**

处理技巧：哈希表，双指针，下面两个题目代表了两类经典题目

### 删除链表的倒数第N个节点，哈希表或者双指针都可以，双指针米奇妙妙屋!

``

    class Solution:
        def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
            
            dummy = ListNode(0)
            dummy.next = head
            first, second = dummy, dummy
            count = 0
            while first:
                if count > n:
                    second = second.next
                first = first.next
                count += 1
    
            second.next = second.next.next
            return dummy.next

``

### 反转链表2

``

    class Solution:
        def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
    
            dummy = ListNode(0)
            dummy.next = head
            pre = dummy
            count = 1
            curr = pre.next
            pre_curr = pre
            while count<=right:
                if count>left:
                    curr_next = curr.next
                    pre_next = pre.next
                    pre.next = curr
                    curr.next = pre_next
                    pre_curr.next = curr_next
                    curr = curr_next
                elif count==left:
                    pre_curr = curr
                    curr = curr.next
                else:
                    pre = curr
                    pre_curr = pre
                    curr = curr.next
                count +=1
            return dummy.next

``

---

## 滑动窗口
题目特征：最长子序列，最长子数组
### 长度最小的子数组

``

    class Solution:
        def minSubArrayLen(self, target: int, nums: List[int]) -> int:
            
            n = len(nums)
            min_length = float('inf')
            each_sum = 0
            start = 0
    
            for i, x in enumerate(nums):
                each_sum += x
                while each_sum>=target:
                    min_length = min(min_length, i-start+1)
                    each_sum -= nums[start]
                    start += 1
            return min_length if min_length<float('inf') else 0
``

---

## 分治
思想：将问题分解为若干个容易处理的子问题，再将子问题的结果合并为整个问题的结果

**需要再做的题目：148**

---

## 双指针

关键点：依据什么样的标准移动左右指针

**需要再做的题目：15**

### 盛最多水的容器

``

    class Solution:
        def maxArea(self, height: List[int]) -> int:
            l = 0
            r = len(height)-1
            res = 0
            while l<r:
                res = max(res, (r-l)*min(height[l], height[r]))
                if height[l]<height[r]:
                    l+=1
                else:
                    r-=1
            return res

``

---

## 最短路径


### 网络延迟时间

dijkstra算法：用来解决单向或者双向有权图的最短路径（无权图使用广度优先搜索）

''
    class Solution:
        def networkDelayTime(self, times: List[List[int]], n: int, k: int) -> int:
            matrix = [[float('inf')]*n for _ in range(n)]
            for a,b,v in times:
                matrix[a-1][b-1] = v
            dis = collections.defaultdict(lambda: float('inf'))
            dis[k-1] = 0
            visited = set()
            while 1:
                if len(visited)==n:
                    return max(list(dis.values()))
                # 每次找出未标记点中距离start最近的点，将其标记
                d = float('inf')
                p = n
                for i in dis.keys():
                    if dis[i]<d and i not in visited:
                        p = i
                        d = dis[i]
                if p==n:
                    break
                visited.add(p)
                dis[p] = d
                # 借助dis[p]更新其他点到start的距离
                for i in range(n):
                    if i in visited:
                        continue
                    dis_i = dis[p]+ matrix[p][i]
                    dis[i] = min(dis[i], dis_i)
            return -1

''
floy算法：更新矩阵matrix，matrix[i][j]表示从i到j的最短路径

''
    matrix = [[float('inf')]*n for _ in range(n)]
            ans = -float('inf')
            for a,b,v in times:
                matrix[a-1][b-1] = v
            for i in range(n):
                matrix[i][i] = 0
            for kk in range(n):
                for i in range(n):
                    for j in range(n):
                        matrix[i][j] = min(matrix[i][j], matrix[i][kk]+matrix[kk][j])
            for i in range(n):
                ans = max(ans, matrix[k-1][i])
            return ans if ans<float('inf') else -1
''
