---
title: Number of Visible People in a Queue
date: 2025-09-16 19:29:52
categories:
    - leetcode
---


# Number of Visible People in a Queue

## 題目敘述

There are `n` people standing in a queue, and they numbered from `0` to `n - 1` in left to right order. You are given an array `heights` of distinct integers where `heights[i]` represents the height of the `i` person.

A person can see another person to their right in the queue if everybody in between is shorter than both of them. More formally, the ith person can see the `j` person if `i < j` and `min(heights[i], heights[j]) > max(heights[i+1], heights[i+2], ..., heights[j-1])`.

Return an array `answer` of length `n` where `answer[i]` is the *number of people* the ith person can `see` to their right in the queue.

### 解法 
第 i 個人是否能看到第 j 個人，基於中間是否有比第 i 個人以及第 j 個人高。
每個人一定可以看的到右方的人，除非沒有人。我們可以由最左邊的人開始。

一排中有 `len` 個人
`int len = heights.size()`

答案為長度 n 的 `vector<int>answer`

首先我們先創建一個 stack 來表示阻擋視線的人
`stack<int> st`


```
// 接著我們從最右邊開始往回到最左邊來去計算，每個人可以看到多少人
for(int i = len - 1; i >= 0; i--){
    int count = 0;
    // 如果右邊有人，且第 i 個人比阻擋視線中來的高，則更左邊的人只能看到這個阻擋視線的人
    while(!st.empty() && (heights[i] > heights[st.top()])){
        st.pop();
        count++;
    }
    // 如果 stack 非空，還能看到阻擋視線的人
    if(!st.empty()){
        count++;
    }
    ans[i] = count;
    st.push(i);
}
return ans;
```