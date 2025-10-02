---
title: Binary Search
date: 2025-09-23 10:51:38
tags:
    - binary search
---

二元搜尋法(binary search)用於尋找有序陣列中目標值位置的搜尋演算法。

二元搜尋比較目標值與陣列中間的元素的大小，如果兩者不相等則會捨棄不可能包含目標值那一半區間，然後在剩餘區間重複此過程，
每次選取新的中間元素並與目標值比較，直至找到目標或區間為空。若區間為空，則說明目標值不存在。

worst case 狀況時間複雜度為 $O(logn)$，其中 n 為元素數量。

二分搜尋有許多其他形式。例如，分數級聯能加快在多個陣列中尋找同一數值的速度，還能高效地解決計算幾何等領域的搜尋問題；
指數搜尋則將搜尋範圍擴充至無界列表。二元搜尋樹和B樹等資料結構的實現也基於二分搜尋原理。

```cpp
#include <vector>
using namespace std;

int binary_search(vector<int>& arr, int target) {
    int L = 0;
    int R = arr.size() - 1;
    
    while (L <= R) {
        int m = (L + R) / 2;
        
        if (arr[m] < target) {
            L = m + 1;
        }
        else if (arr[m] > target) {
            R = m - 1;
        }
        else {
            return m;
        }
    }
    
    return -1;
}
```