# 11. Container With Most Water

**LeetCode:** https://leetcode.com/problems/container-with-most-water/  
**Difficulty:** Medium  
**Status:** Solved independently  
**Approach:** Two Pointers

## Problem

Choose two vertical lines from the height array that hold the most water. The container area is:

`area = (right - left) * Math.min(height[left], height[right])`

## My original JavaScript solution

```javascript
/**
 * @param {number[]} height
 * @return {number}
 */
var maxArea = function(height) {
    let maxArea = 0;
    let start = 0;
    let end = height.length - 1;
    while(start < end){
        let area = (end - start) * Math.min(height[start] , height[end]);
        maxArea = Math.max(maxArea , area);
        if(height[start] <= height[end]){
            start = start + 1;
        }else end--;
    }

    return maxArea;
};
```

## Explanation

1. Start with pointers at opposite ends: `start = 0` and `end = height.length - 1`. This gives the maximum initial width.
2. Calculate the current area using the shorter line, because water cannot rise above that line.
3. Keep the maximum area seen so far.
4. Move **the pointer with the shorter height** toward the center.
5. Stop when the pointers meet, then return the maximum.

### Why move the shorter pointer?

Suppose the left height is 1 and the right height is 7. Keeping the left height of 1 while moving the right pointer inward reduces width, while the limiting height cannot exceed 1. So that move cannot improve the result. Moving the shorter line is the only move that might find a taller limiting height.

When heights are equal, either pointer can move safely. Your code chooses `start++`.

## Diagram

```text
index:   0  1  2  3  4  5  6  7  8
height: [1, 8, 6, 2, 5, 4, 8, 3, 7]
         ^                       ^
       start                    end

First area = (8 - 0) * min(1, 7) = 8.
Move start because 1 < 7.

Next:
height: [1, 8, 6, 2, 5, 4, 8, 3, 7]
            ^                    ^
          start                 end

Area = (8 - 1) * min(8, 7) = 49.
```

## Full dry run

| Step | start | end | Area | Maximum | Move |
|---:|---:|---:|---:|---:|---|
| 1 | 0 | 8 | 8 | 8 | start++ |
| 2 | 1 | 8 | 49 | 49 | end-- |
| 3 | 1 | 7 | 18 | 49 | end-- |
| 4 | 1 | 6 | 40 | 49 | start++ |
| 5 | 2 | 6 | 24 | 49 | start++ |
| 6 | 3 | 6 | 6 | 49 | start++ |
| 7 | 4 | 6 | 10 | 49 | start++ |
| 8 | 5 | 6 | 4 | 49 | start++ |

Pointers meet at index 6; answer = **49**.

## Test cases

| Input | Expected output |
|---|---:|
| `[1,8,6,2,5,4,8,3,7]` | 49 |
| `[1,1]` | 1 |
| `[4,3,2,1,4]` | 16 |
| `[1,2,1]` | 2 |

## Complexity

- **Time: O(n)** — each iteration moves one pointer inward, at most n - 1 times.
- **Extra space: O(1)** — only a few numeric variables.

## Edge cases

- Only two heights: calculate their single possible area.
- Equal heights: either pointer can move.
- Zero heights: area may be zero; algorithm still works.
- Do not sort: positions determine the width.

## Interview takeaway

A narrower container can only beat the current area if it has a larger limiting height. Therefore discard the shorter line.

[← Two Pointers sheet](README.md) · [Dashboard](../DASHBOARD.md)
