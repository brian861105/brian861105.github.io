---
title: Trapping Rain Water
date: 2025-09-19 15:45:09
tags:
    - Two Pointers
categories:
    - leetcode
---

# [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water)

## Problem Description

Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

## Approach: Two Pointers

### Intuition
The key insight is that at any position, the water level is dtermined the minimum of the maximum heights to its left and right. We can use two pointers moving towards each other from both ends.

### Algorithm
1. Initialize two pointer `left` and `right` at the beginning and each of the array.
2. Keep track of `leftMax` and `rightMax`, the maximum heights seen so far from left and right respectively.
3. Move the pointer with smaller height:
    - If height[left] < height[right], process the left side
    - The water level at position `left` is determined by `leftMax` (since we know `rightMax` is at least `height[right]` which is greater)
    - If current height is less than `leftMax`, we can trap `leftMax - heights[left]` water
    - Move `left` pointer forward
4. Apply the same logic for the right side
5. Continue until pointers meet
```Cpp
class Solution {
    public:
        int trap(vector<int>& height){
            int len = height.size();
            int left = 0;
            int right = len - 1;
            int leftMax = 0;
            int rightMax = 0;
            int water = 0;
            while(left < right){
                if(height[left] < height[right]){
                    if(height[left] >= leftMax){
                        leftMax = height[left];
                    }else{
                        water += leftMax - height[left];    
                    }
                    left++;
                }else{
                    if(height[right] >= rightMax){
                        rightMax = height[right];
                    }else{
                        water += rightMax - height[right];
                    }
                    right--;
                }
            }
            return water;
        }
};
```