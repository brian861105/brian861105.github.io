---
title: Word Break
date: 2024-10-05 11:19:31
tags:
  - leetcode
  - dynamic programming
---
# Word Break

## 題目敘述

Given a string `s` and `a` dictionary of strings `wordDict`, return true if `s` can be segmented into a space-separated sequence of one or more dictionary words.

**Note** that the same word in the dictionary may be reused multiple times in the segmentation.

### Example1

**Input**: s = "leetcode", wordDict = ["leet","code"]
**Output**: true
**Explanation**: Return true because "leetcode" can be segmented as "leet code".

### Example 2:

**Input**: s = "applepenapple", wordDict = ["apple","pen"]
**Output**: true
**Explanation**: Return true because "applepenapple" can be segmented as "apple pen apple".
Note that you are allowed to reuse a dictionary word.

### Example 3:

**Input**: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
**Output**: false
 
### 解法 

每個單字可以重複，因此我們需要紀錄的是在第 n 個的字母時，是否為合法，如果為合法我們就可以從 wordDict 中找到最適合他的

首先我們先將 wordDict 中的每個單字，根據字首分門別類

```
unordered_map<char, vector<string>> map;

for(auto word:wordDict){
    map[word[0]].push_back(word);
}
```

我們接著將 string 利用 dp 來說明第 n 個字母是否合法
```
vector<bool> dp(s.size() + 1, false);

dp[0] = true;

for(int i = 0; i < s.size(); i++){
    if(dp[i]){ // 如果第 i 個字母為合法，則我們從 wordDict 中挑選一個合法的
        for(auto word:map[s[i]]){
            if(word == s.substr(i, word.size())){
                dp[i + word.size()] = true;
            }           
        }
    }
}

return dp[s.size()]; // 最後將最後一個字母結果回傳
```
