+++
title = "Basic template"
date = 2021-02-20 17:21:06
slug = "202102201721"

[taxonomies]
tags = ["Cram" ]
categories = ["Cram"]

+++

<!-- more -->

# 算法

## 二分

```c++
int l = 0, r = MAXN - 1;
while (l < r) {
	int mid = l + r >> 1;
	if (check(mid))
		r = mid;
	else
        l = mid + 1;
}
return left;
```

### **搜索区间**

两端都闭 `[left, right]` 和左闭右开 `[left, right)` 的区别

- 两端都闭 [2, 3] 会搜索 2,3 [2, 2] 会搜索 2 [3, 2] 时无效跳出循环
- 左闭右开 [2, 3) 会搜索 2 [2, 2] 时无效跳出循环
- 如果排序数组中是无重复元素的，果断用「寻找一个数」的左闭右闭模版

两端都闭 `[left, right]` 搜索区间设置方法：

```
int left = 0;
int right = nums.size() - 1;
while (left <= right) {return mid;}
```

左闭右开 `[left, right)` 搜索区间设置方法：

```
int left = 0;
int right = nums.size();
while (left < right) {}
return left;
```

### lower 和 upper bound

例题：[在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if(nums.empty()) return {-1,-1};
    
        int l = 0, r = nums.size() - 1; //二分范围
        while( l < r)			        //查找元素的开始位置
        {
            int mid = (l + r )/2;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        if( nums[r] != target) return {-1,-1};  //查找失败
        int L = r;
        l = 0, r = nums.size() - 1;     //二分范围
        while( l < r)                   //查找元素的结束位置
        {
            int mid = (l + r + 1)/2;
            if(nums[mid] <= target ) l = mid;
            else r = mid - 1;
        }
        return {L,r};
    }
};
```

### 查找旋转数组

#### [最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii)

```c++
int findMin(vector<int>& nums) {
        int left = 0;
        int right = nums.size()-1;
        while(left<right){
            int mid = left + (right-left)/2;
            if(nums[mid] > nums[right]){
                left = mid+1;
            }
            else if(nums[mid] < nums[right]){
                right = mid;
            }
            else{
                right --;
            }
        }
        return nums[left];
    }
```

#### 用最大值求最小值

最大值右边就是最小值

```c++
int findMin(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left + 1) / 2;   /* 先加一再除，mid更靠近右边的right */
            if (nums[left] < nums[mid]) {
                left = mid;                            /* 向右移动左边界 */
            } else if (nums[left] > nums[mid]) {
                right = mid - 1;                       /* 向左移动右边界 */
            }
        }
        return nums[(right + 1) % nums.size()];    /* 最大值向右移动一位就是最小值了（需要考虑最大值在最右边的情况，右移一位后对数组长度取余） */
    }
```



## 归并

[148. 排序链表 - LeetCode](https://leetcode-cn.com/problems/sort-list/)

```c++
class Solution {
public:
    ListNode* merge(ListNode* head, ListNode* head2){
        if(head==nullptr) return head2;
        if(head2==nullptr) return head;
        if(head->val <= head2->val){
            head->next = merge(head->next, head2);
            return head;
        }
        else{
            head2->next = merge(head, head2->next);
            return head2;
        }
    }
    ListNode* sortList(ListNode* head) {
        if(head==nullptr||head->next==nullptr) return head;
        ListNode* slow=head;
        ListNode* fast=head->next;
        while(fast!=nullptr&&fast->next!=nullptr){
            slow=slow->next;
            fast=fast->next->next;
        }
        ListNode* head2=slow->next;
        slow->next=nullptr;
        return merge(sortList(head),sortList(head2));
    }
};
```

### 归并排序模板

```java
    //归并排序入口
    public void mergeSort(int[] nums){
        MergeSort(nums,0,nums.length-1);
    }

    /**
     * 归并排序
     * @param nums 待排序数组
     * @param start 数组开始的下标
     * @param end 数组结束的下标
     */
    private void MergeSort(int[] nums,int start,int end){
        if(start<end){
            int mid=start+(end-start)/2;
            MergeSort(nums,start,mid); //将无序数组划分
            MergeSort(nums,mid+1,end); //将无序数组划分
            merge(nums,start,mid,end); //再将两个有序数组合并
        }
    }

    /**
     *  双指针合并两个有序数组
     * @param nums
     * @param start
     * @param mid
     * @param end
     */
    private void merge(int[]nums, int start, int mid, int end){
        int P1=start;
        int P2=mid+1;
        int tmp[]=new int[end-start+1]; //需要借助额外的O(n)空间来存储合并后的数组
        int cur=0;
        while (P1<=mid&&P2<=end){
            if(nums[P1]<nums[P2]){
               tmp[cur]=nums[P1];
               P1++;
            }else {
               tmp[cur]=nums[P2];
               P2++;
            }
            cur++;
        }
        while (P1<=mid){
            tmp[cur]=nums[P1];
            P1++;
            cur++;
        }
        while (P2<=end){
            tmp[cur]=nums[P2];
            P2++;
            cur++;
        }
        for (int i = 0; i < res.length ; i++) {
             nums[i+start]=tmp[i];
        }
    }
```



## 滑动窗口

```c++
void slidingWindow(string s, string t) {
    int left = 0;
    int right = 0;
    int valid = 0; // 窗口内已凑齐的字符种类数量
    unordered_map<char, int> window;
    unordered_map<char, int> need; // 需要凑齐的字符和对应数量
    for (char c : t) need[c]++;

    while (right < s.size()) {
        // 右边届入窗口，进行窗口内数据的一系列更新
        window[s[right]]++;
        if (window[s[right]] == need[s[right]]) {
            valid++;
        }  // 注意：先加，再判断

        // 判断左侧窗口是否要收缩
        while (window needs shrink) {
            // 左边界移出窗口，进行窗口内数据的一系列更新
            if (window[s[left]] == need[s[left]]) {
                valid--;
            }  // 注意：先判断，再减
            window[s[left]]--;
            left++;  // 注意：左边届的收缩，要写在所有处理完成的最后
        }
        // 采集答案...
        right++;  // 注意：右边届的收缩，要写在所有处理完成的最后
    }
}
```



## 回溯

```
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

### 回溯算法几种问题的复杂度分析

#### 子集问题分析：

**时间复杂度**： ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5Ctimes2%5En%29) 。因为每一个元素的状态无外乎取与不取，一共![[公式]](https://www.zhihu.com/equation?tex=2%5En) 种状态，每种状态都需要 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) 的构造时间，最终时间复杂度为 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5Ctimes2%5En%29) 。

**空间复杂度**： ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) ，递归深度为n，所以系统栈所用空间为 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) 。

#### 排列问题分析：

**时间复杂度：**： ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5Ctimes+n%21%29) 。因为一共![[公式]](https://www.zhihu.com/equation?tex=n%21) 种排列，每种排列都需要 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) 的构造时间，最终时间复杂度为 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5Ctimes+n%21%29) 。

**空间复杂度**： ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) ，递归深度为n，所以系统栈所用空间为 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) 。

#### 组合问题分析：

**时间复杂度：** ![[公式]](https://www.zhihu.com/equation?tex=O%28C_n%5Ek+%5Ctimes+k%29) ，总共有 ![[公式]](https://www.zhihu.com/equation?tex=C_n%5Ek) 种组合，每种组合需要 ![[公式]](https://www.zhihu.com/equation?tex=O%28k%29) 的时间复杂度。另一方面，组合问题其实就是一种子集的问题，所以组合问题最坏的情况，也不会超过子集问题的时间复杂度 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5Ctimes+2%5En%29) 。

**空间复杂度**： ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) ，递归深度为n，所以系统栈所用空间为 ![[公式]](https://www.zhihu.com/equation?tex=O%28n%29) 。

#### N皇后问题分析

**时间复杂度：** ![[公式]](https://www.zhihu.com/equation?tex=O%28N%21%29) ，其中 N 是皇后数量，由于每个皇后必须位于不同列，因此已经放置的皇后所在的列不能放置别的皇后。第一个皇后有 N 列可以选择，第二个皇后最多有 N-1列可以选择...。

**空间复杂度**： ![[公式]](https://www.zhihu.com/equation?tex=O%28N%29) ，递归深度为n，所以系统栈所用空间为 ![[公式]](https://www.zhihu.com/equation?tex=O%28N%29) 。

#### 解数独问题分析

**时间复杂度：** ![[公式]](https://www.zhihu.com/equation?tex=O%289%5Em%29) ，m是'.'的数目。

**空间复杂度：** ![[公式]](https://www.zhihu.com/equation?tex=O%28n%5E2%29) ，n是数独盘子的大小，递归的深度是 ![[公式]](https://www.zhihu.com/equation?tex=n%5E2) 。

### 子集

输入一个不包含重复数字的数组，算法输出这些数字的所有子集。

```c++
vector<vector<int>> subsets(vector<int>& nums) {
    // base case，返回一个空集
    if (nums.empty()) return {{}};
    // 把最后一个元素拿出来
    int n = nums.back();
    nums.pop_back();
    // 先递归算出前面元素的所有子集
    vector<vector<int>> res = subsets(nums);

    int size = res.size();
    for (int i = 0; i < size; i++) {
        // 然后在之前的结果之上追加
        res.push_back(res[i]);
        res.back().push_back(n);
    }
    return res;
}
```

时间复杂度：总的迭代次数应该是 2^N，因为 `res[i]` 也是一个数组，`push_back` 把 `res[i]` copy 一份然后添加到数组的最后，所以一次操作的时间是 O(N)。总的时间复杂度就是 O(N*2^N)。

空间复杂度，如果不计算储存返回结果所用的空间的，只需要 O(N) 的递归堆栈空间。如果计算 `res` 所需的空间，应该是 O(N*2^N)。

**第二种通用方法是回溯算法**

```c++
vector<vector<int>> res;

vector<vector<int>> subsets(vector<int>& nums) {
    // 记录走过的路径
    vector<int> track;
    backtrack(nums, 0, track);
    return res;
}

void backtrack(vector<int>& nums, int start, vector<int>& track) {
    res.push_back(track);
    // 注意 i 从 start 开始递增
    for (int i = start; i < nums.size(); i++) {
        // 做选择
        track.push_back(nums[i]);
        // 回溯
        backtrack(nums, i + 1, track);
        // 撤销选择
        track.pop_back();
    }
}
```

对 `res` 的更新是一个**前序遍历**，`res` 就是树上的所有节点

### 组合

输入两个数字 `n, k`，算法输出 `[1..n]` 中 k 个数字的所有组合。

直接套回溯算法模板，`k` 限制了树的高度，`n` 限制了树的宽度

```c++
vector<vector<int>>res;

vector<vector<int>> combine(int n, int k) {
    if (k <= 0 || n <= 0) return res;
    vector<int> track;
    backtrack(n, k, 1, track);
    return res;
}

void backtrack(int n, int k, int start, vector<int>& track) {
    // 到达树的底部
    if (k == track.size()) {
        res.push_back(track);
        return;
    }
    // 注意 i 从 start 开始递增
    for (int i = start; i <= n; i++) {
        // 做选择
        track.push_back(i);
        backtrack(n, k, i + 1, track);
        // 撤销选择
        track.pop_back();
    }
}
```

`backtrack` 函数和计算子集的差不多，区别在于，更新 `res` 的地方是树的底端。

### 排列

输入一个不包含重复数字的数组 `nums`，返回这些数字的全部排列。

```c++
vector<vector<int>>res;

vector<vector<int>> permute(vector<int>& nums) {
    if (k <= 0 || n <= 0) return res;
    vector<int> track;
    backtrack(nums, track);
    return res;
}

void backtrack(vector<int>& nums, vector<int>& track) {
    // 到达树的底部
    if (track.size() == nums.size()) {
        res.push_back(track);
        return;
    }
    for (int i = 0; i < nums.size(); i++) {
        // 排除不合法的选择
        if(count(track.begin(), track.end(), nums[i])){
            continue;
        }
        // 做选择
        track.push_back(nums[i]);
        // 进入下一层决策树
        backtrack(nums, track);
        // 撤销选择
        track.pop_back();
    }
}
```

排列问题的树比较对称，而组合问题的树越靠右节点越少。

在代码中的体现就是，排列问题每次通过排除在 `track` 中已经选择过的数字；而组合问题通过传入一个 `start` 参数，来排除 `start` 索引之前的数字。

## 前缀和

前缀和对于一个给定的数组`nums`，额外开辟一个前缀和数组进行预处理

例题：算出一共有几个和为 k 的子数组

```c++
int subarraySum(vector<int>& nums, int k) {
    int n = nums.size();
    //前缀和：该前缀和出现的次数
    unordered_map<int, int> preSum;
    // base case
    preSum[0] = 1;

    int ans = 0, sum0_i = 0;
    // 穷举所有子数组
    for (int i = 0; i < n; i++){
        sum0_i += nums[i];
        // 目标前缀和 nums[0...j]
        int sum0_j = sum0_i - k;
        // 如果前面出现过这个前缀和，直接更新答案
        if(preSum.count(sum0_j)){
            ans += preSum[sum0_j];
        }
        preSum[sum0_i]++;
    }
        

    return ans;
}
```

例题：统计班上同学考试成绩在不同分数段的百分比

```java
int[] scores; // 存储着所有同学的分数
// 试卷满分 150 分
int[] count = new int[150 + 1]
// 记录每个分数有几个同学
for (int score : scores)
    count[score]++
// 构造前缀和
for (int i = 1; i < count.length; i++)
    count[i] = count[i] + count[i-1];
```

