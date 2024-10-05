---
title: Longest Common Subsequence
date: 2024-10-05 01:56:56
tags: 
  - leetcode
  - dynamic programming
---
# Longest Common Subsequence

## 題目敘述

Given two strings `text1` and `text2`, return the length of their longest common subsequence. If there is no common subsequence, return 0.

### Definitions

#### Subsequence

A **subsequence** of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

- For example, "ace" is a subsequence of "abcde".

#### Common Subsequence

A **common subsequence** of two strings is a subsequence that is common to both strings.

### Task

Implement a function that finds the length of the longest common subsequence between two given strings.

### Example1

**Input**: text1 = "abcde", text2 = "ace" 
**Output**: 3 
**Explanation**: The longest common subsequence is "ace" and its length is 3.

### Example 2:

**Input**: text1 = "abc", text2 = "abc"
**Output**: 3
**Explanation**: The longest common subsequence is "abc" and its length is 3.

### Example 3:

**Input**: text1 = "abc", text2 = "def"
**Output**: 0
**Explanation**: There is no such common subsequence, so the result is 0.
 
 
### 解法 

Dynamic Program

因為此題不需要連續的字母，因此我們可以使用dynamic program

let dp 為 (text1.size + 1) * (text2.size + 1) 大小的 矩陣

當 text1 第 n 個字母 以及 text2 第 m 個字母的時候，我們需要比較兩者是否相同，反之則取 第 n - 1 個字母對第 m 個字母 和 第 n 個字母對第 m - 1 個字母 所得到的最大 Common Subsequence 的長度
```
if text1[n - 1] == text2[m - 1]

dp[n][m] = dp[n - 1][m - 1] + 1

else

dp[n][m] = max(dp[n-1][m], dp[n][m-1])
```

在C++ 我們可以這樣寫
```
    int longestCommonSubsequence(string text1, string text2) {
        int text1Length = text1.size();
        int text2Length = text2.size();

        int dp[text1Length + 1][text2Length + 1];

        memset(dp, 0, sizeof(dp));

        for(int i = 1; i <= text1Length; i++){
            for(int j = 1; j <= text2Length; j++){
                if(text1[i - 1] == text2[j - 1]){
                dp[i][j] = dp[i - 1][j - 1] + 1;
                }
                else{
                    dp[i][j] = max(dp[i - 1][j], dp[i][j-1]);
                }
            }
        }
        return dp[text1Length][text2Length];
    }
```