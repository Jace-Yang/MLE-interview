# 专题：数组中的DFS

### 基础：全排列

```python
class Solution:
    ans = []
    ans_list = []
    def permute(self, nums):
        self.ans = []
        self.ans_list = []
        visited = [0]*len(nums)
        self.DFS(nums,0,visited)
        return self.ans_list


    def DFS(self,nums,idx,visited):
        if idx == len(nums):
            self.ans_list.append(self.ans[:])
            return
        for i in range(len(nums)):
            if visited[i]:
                continue
                
            visited[i] = 1
            self.ans.append(nums[i])
            self.DFS(nums,idx+1,visited)
            self.ans.pop() ##回溯
            visited[i] = 0
```



#### 22. 括号生成

数字 `n` 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```python
class Solution:
    ans = []
    ans_list = []
    def generateParenthesis(self, n: int):
        self.ans = []
        self.ans_list = []
        self.DFS(0,0,n)
        return self.ans_list

    def DFS(self,left_num,right_num,n):
        if right_num > left_num:
            return
        if left_num == n and right_num == n:
            self.ans_list.append(''.join(self.ans))
            return
        if right_num > n or left_num > n:
            return
        ##### 左括号
        self.ans.append("(")
        self.DFS(left_num+1,right_num,n)
        self.ans.pop()
        ##### 右括号
        self.ans.append(")")
        self.DFS(left_num,right_num+1,n)
        self.ans.pop()
```

#### 39. 组合总和

给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。

candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。 

```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
```

**解法：** 为了保证“不重复”，例如不能有[2,2,3]和[3,2,2], 需要做到“不吃回头草”，不能再加入前面已经遍历过的数字。所以，在每个位置，要么选它，即now+candidate[idx]；要么不选它，即idx+1. 

```python
class Solution:
    ans = []
    ans_list = []
    def combinationSum(self, candidates, target: int):
        self.ans = []
        self.ans_list = []
        self.DFS(candidates,target,0,0)
        return self.ans_list

    def DFS(self,candidates,target,idx,now):
        if now == target:
            self.ans_list.append(self.ans[:])
            return
        if now > target:
            return
        if idx >= len(candidates):
            return
        #### 选！
        self.ans.append(candidates[idx])
        self.DFS(candidates,target,idx,now+candidates[idx])
        self.ans.pop()
        ### 不选！
        self.DFS(candidates,target,idx+1,now)

```

