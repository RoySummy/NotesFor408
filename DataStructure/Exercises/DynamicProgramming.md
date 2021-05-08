# 动态规划

##  1. 删除并获得点数

给你一个整数数组 nums ，你可以对它进行一些操作。

每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除每个等于 nums[i] - 1 或 nums[i] + 1 的元素。

开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。

### 示例 1：

>输入：nums = [3,4,2]
>输出：6
>解释：
>删除 4 获得 4 个点数，因此 3 也被删除。
>之后，删除 2 获得 2 个点数。总共获得 6 个点数。

### 示例 2：

>输入：nums = [2,2,3,3,3,4]
>输出：9
>解释：
>删除 3 获得 3 个点数，接着要删除两个 2 和 4 。
>之后，再次删除 3 获得 3 个点数，再次删除 3 获得 3 个点数。
>总共获得 9 个点数。

### 提示：

> 1 <= nums.length <= 2 * 104
> 1 <= nums[i] <= 104

### 题解与思路

```c++
/// leetcode 740
///题目翻译完之后结果和 “打家劫舍” 那道动态规划题的思路是一样的，维护之前一个位置与之前两个位置的最大值
///但是刚开始没有通过是因为自己只考虑了数组中存在的数的情况，数组中不存在的数没有考虑到
///找到数组中的最大值，从1遍历到最大值，数组中不存在的数就当0算(map特性)不影响结果
int deleteAndEarn(vector<int>& nums) {
    map<int, int> mp;
    int maximum = nums[0];
    for (auto e : nums)
    {
        maximum = max(e, maximum);
        mp[e] += e;
    }
    if (mp.size() == 1)
        return mp[mp.begin()->second];
    int first = mp[1];
    int second = max(mp[1], mp[2]);
    for (int i = 3; i <= maximum; i++)
    {
        int t = second;
        second = max(mp[i] + first, second);
        first = t;
    }
    return second;
}
```

