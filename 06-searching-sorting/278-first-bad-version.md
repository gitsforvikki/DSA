# 278. First Bad Version — Three Binary Search Solutions

[LeetCode 278](https://leetcode.com/problems/first-bad-version/) | JavaScript | Binary Search

## Problem

Versions are numbered from 1 to n. Once a version is bad, all later versions are bad. The API `isBadVersion(version)` returns true for bad versions. Find the **first bad version**.

Example: `1:G  2:G  3:G  4:B  5:B  6:B  7:B  8:B` → answer: **4**.

## Solution 1 — Exclude mid when bad (my first solution)

```javascript
var solution = function(isBadVersion) {
    return function(n) {
        let left = 1;
        let right = n;
        while (left <= right) {
            let mid = left + Math.floor((right - left) / 2);
            let isBad = isBadVersion(mid);
            if (isBad) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return left;
    };
};
```

When mid is bad, move `right` before mid. When mid is good, move `left` after mid. At the end, `left` is the first bad version, even if that version was excluded earlier. This is a **lower-bound / first-true** binary search.

| Step | left | right | mid | Result | Update |
|---|---:|---:|---:|---|---|
| 1 | 1 | 8 | 4 | bad | right = 3 |
| 2 | 1 | 3 | 2 | good | left = 3 |
| 3 | 3 | 3 | 3 | good | left = 4 |
| End | 4 | 3 | — | — | return 4 |

## Solution 2 — My custom termination condition

This keeps my original `while (left <= right)` and `right = mid` idea. The explicit equality check prevents a single-element interval from repeating.

```javascript
var solution = function(isBadVersion) {
    return function(n) {
        let left = 1;
        let right = n;
        while (left <= right) {
            let mid = left + Math.floor((right - left) / 2);
            let isBad = isBadVersion(mid);
            if (right == left && right == mid) return mid;
            if (isBad) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
    };
};
```

**How it works:** When `mid` is bad, it might be the first bad version, so keep it in the search range with `right = mid`. When it is good, exclude it with `left = mid + 1`. When only one candidate remains, the equality check returns it.

| Step | left | right | mid | Result | Update |
|---|---:|---:|---:|---|---|
| 1 | 1 | 8 | 4 | bad | right = 4 |
| 2 | 1 | 4 | 2 | good | left = 3 |
| 3 | 3 | 4 | 3 | good | left = 4 |
| 4 | 4 | 4 | 4 | bad | return 4 |

**JavaScript note:** `right == left && right == mid` works, but `right === left && left === mid` is preferable. Do **not** write `right == left == mid`, because JavaScript interprets that as `(right == left) == mid`. The API call on the final iteration is unnecessary; Solution 3 avoids it.

## Solution 3 — Keep mid, stop when pointers meet (cleaner)

```javascript
var solution = function(isBadVersion) {
    return function(n) {
        let left = 1;
        let right = n;
        while (left < right) {
            let mid = left + Math.floor((right - left) / 2);
            if (isBadVersion(mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    };
};
```

The answer always stays in the range `[left, right]`. If mid is bad, keep mid because it may be the first bad. If mid is good, move past it. When `left === right`, the range contains exactly one version: the answer.

| Step | left | right | mid | Result | Update |
|---|---:|---:|---:|---|---|
| 1 | 1 | 8 | 4 | bad | right = 4 |
| 2 | 1 | 4 | 2 | good | left = 3 |
| 3 | 3 | 4 | 3 | good | left = 4 |
| End | 4 | 4 | — | — | return 4 |

## Difference between all three

| Feature | Solution 1 | Solution 2 | Solution 3 |
|---|---|---|---|
| Loop | `left <= right` | `left <= right` | `left < right` |
| When mid is bad | `right = mid - 1` | `right = mid` | `right = mid` |
| When mid is good | `left = mid + 1` | `left = mid + 1` | `left = mid + 1` |
| Exit | Pointers cross | Explicit return inside loop | Pointers meet |
| Return | `left` | `mid` | `left` |
| Extra final API call | No | Yes | No |

### Visual intuition

```text
Versions:    1   2   3   4   5   6   7   8
Status:      G   G   G   B   B   B   B   B
                         ^
                     first bad

Solution 1: right moves to 3, left eventually moves to 4.
Solution 2: right stays at 4, equality condition returns 4.
Solution 3: right stays at 4, loop ends when left == right == 4.
```

## Complexity

All three: **O(log n)** time and **O(1)** extra space. Solution 2 may make one extra `isBadVersion` call at the end.

## Edge cases

- `n = 1` and first bad = 1 → answer 1.
- First version is bad → answer 1.
- Last version is first bad → answer n.
- Large n → use `left + Math.floor((right - left) / 2)`.

**Interview takeaway:** Choose loop termination and pointer updates as a pair. For `right = mid`, prefer `while (left < right)`. For `right = mid - 1`, `while (left <= right)` works naturally.
