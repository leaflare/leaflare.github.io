+++
title = "算法题trick"
date = 2021-04-20 17:21:06
slug = "202104201721"

[taxonomies]
tags = ["Cram" ]
categories = ["Cram"]

+++

<!-- more -->

# 边界情况处理



### 哨兵

例题：[84. 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

```c++
#include <iostream>
#include <vector>
#include <stack>

using namespace std;


class Solution {
public:
    int largestRectangleArea(vector<int> &heights) {
        unsigned long size = heights.size();
        if (size == 1) {
            return heights[0];
        }
        int res = 0;
        stack<int> stk;
        for (int i = 0; i < size; ++i) {
            while (!stk.empty() && heights[stk.top()] > heights[i]) {
                int length = heights[stk.top()];
                stk.pop();
                int weight = i;
                if (!stk.empty()) {
                    weight = i - stk.top() - 1;
                }
                res = max(res, length * weight);
            }
            stk.push(i);
        }
        while (!stk.empty()) {
            int length = heights[stk.top()];
            stk.pop();
            int weight = size;
            if (!stk.empty()) {
                weight = size - stk.top() - 1;
            }
            res = max(res, length * weight);
        }
        return res;
    }
};

```

以上参考代码 需要考虑两种特殊的情况：

弹栈的时候，栈为空；
遍历完成以后，栈中还有元素；
为此可以我们可以在输入数组的两端加上两个高度为 0 （只要比 1 严格小都行）的柱形，可以回避上面这两种分类讨论。

这两个站在两边的柱形就是哨兵（Sentinel）。

有了这两个柱形：

左边的柱形（第 1 个柱形）由于它一定比输入数组里任何一个元素小，它肯定不会出栈，因此栈一定不会为空；

右边的柱形（第 2 个柱形）也正是因为它一定比输入数组里任何一个元素小，它会让所有输入数组里的元素出栈（第 1 个哨兵元素除外）。

#### **使用哨兵精简后的代码**

```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int ans = 0;
        heights.insert(heights.begin(), 0);  
        // 第一个元素为添0，可以避免栈为空的情况，下面的while不用判断 !stack.empty()，0可看作是所有元素中最小的值
        heights.push_back(0);                // 最后一个元素添0，是为了触发最后一个单调栈开始收缩
        vector<int> stack = {0};             // 第一个元素0，先入栈，是为了while里面的 stack.back() 第一次能取到值
        for (int i = 1; i < heights.size(); i++) {
            // 此时i指向新元素，当新元素小于此时的栈顶（i-1）时，单调栈开始收缩，
            // 第一个老栈顶肯定为i-1，否则不符合逻辑
            while (heights[i] < heights[stack.back()]) {
                int curHeight = heights[stack.back()];
                stack.pop_back();
                int right = i - 1;            
                // 右边届是固定的，为第一个老栈顶，也是最初栈里面的最高点
                int left = stack.back() + 1;  
                // 每次更新左边，此时栈已经pop过了，所以栈顶为当前矩形左边届的前一个
                // +1这个位置可能就不在栈里，但是这个值一定大于curHeight，一定要用它做左边
                ans = max(ans, (right - left + 1) * curHeight);
            }
            stack.push_back(i);
        }
        return ans;
    }
};
```

