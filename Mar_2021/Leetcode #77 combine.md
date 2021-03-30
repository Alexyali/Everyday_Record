# Leetcode #77 combine

给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合。

思路：回溯法

定义`dfs(n,first,k)`函数，其含义为给定整数n，当前选择的数字是first，剩余需要选择的数字个数为k。初始问题即为`dfs(n,0,k)`。

dfs退出条件为`k==0`，意味着已经选完全部的数字。

每一次进入dfs函数，可以选择的数字范围为[first+1, n-k+1]，每次填入一个数字后，dfs函数中first变为填入的数字，k减1。最后弹出该数字完成回溯。

```c++
class Solution {
    vector<vector<int>> res;
public:
    vector<vector<int>> combine(int n, int k) {
        vector<int> out;
        dfs(out,n,0,k);
        return res;
    }

    void dfs(vector<int>&out, int n, int first, int k) {
        if (k==0) {
            res.push_back(out);
            return;
        }
        for(int i=first+1; i<=n-k+1; i++) {
            out.push_back(i);
            dfs(out,n,i,k-1);
            out.pop_back();
        }
    }
};
```

