# ⭐ IMPORTANT — LeetCode 153: Find Minimum in Rotated Sorted Array

**Pattern:** Binary Search on Rotated Sorted Array · **Difficulty:** Medium · **Status:** Solved (2 approaches)

[Problem on LeetCode](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)

> My two approaches: (1) maintain a minimum while eliminating sorted halves, and (2) find the pivot / point of deflection directly. This problem assumes **distinct elements**.

## Understand the problem

An ascending sorted array has been rotated. Return the **minimum value**, not its index.

```text
Original:   [0, 1, 2, 4, 5, 6, 7]
Rotated:    [4, 5, 6, 7, 0, 1, 2]
                         ^
                    minimum = 0
                         ^
                 pivot / deflection

Index:       0  1  2  3  4  5  6
Values:      4  5  6  7  0  1  2
                       7 → 0: ascending order breaks
```

Because the original array was sorted, at least one side of the midpoint is sorted. We can eliminate a half at every iteration.

---

## Approach 1 — Track minimum with `Math.min()`

### My solution

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var findMin = function(nums) {
    let start = 0;
    let end = nums.length - 1;
    let min = Infinity;

    while (start < end) {
        let mid = start + Math.floor((end - start) / 2);

        if (nums[start] <= nums[mid]) {
            min = Math.min(nums[start], min);
            start = mid + 1;
        } else {
            min = Math.min(nums[mid], min);
            end = mid;
        }
    }

    return Math.min(min, nums[start]);
};
```

### Why each part works

1. **`min = Infinity`:** start with a value larger than any normal array element. Avoid `null`: `Math.min(5, null)` evaluates to `0` in JavaScript.
2. **`nums[start] <= nums[mid]`:** the left half `[start ... mid]` is sorted. Its smallest value is `nums[start]`, so save that candidate and search to the right.
3. **Otherwise:** the rotation/pivot lies in `[start ... mid]`. `nums[mid]` is a valid candidate; retain `mid` using `end = mid`.
4. **`start < end`:** stop when one element remains.
5. **Final `Math.min(min, nums[start])`:** include the last candidate, which the loop did not process.

### Dry run: `[4, 5, 6, 7, 0, 1, 2]`

| Step | start | end | mid | Comparison | min after step | New range |
|---|---:|---:|---:|---|---:|---|
| 1 | 0 | 6 | 3 | 4 <= 7 (left sorted) | 4 | start = 4 |
| 2 | 4 | 6 | 5 | 0 <= 1 (left sorted) | 0 | start = 6 |
| End | 6 | 6 | — | stop | 0 | `Math.min(0, 2) = 0` |

```text
[4  5  6  7] [0  1  2]
 ^        ^   ^
 save 4; discard sorted left, search right

             [0  1] [2]
              ^
            save 0
```

**Time:** O(log n) · **Extra space:** O(1)

---

## Approach 2 — Pivot / Point of Deflection (my latest solution)

### My solution

```javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var findMin = function(nums) {
    let left = 0;
    let right = nums.length - 1;

    while (left <= right) {
        // Current range is already sorted
        if (nums[left] <= nums[right]) return nums[left];

        let mid = left + Math.floor((right - left) / 2);

        // Point of deflection: first element smaller than its predecessor
        if (mid > 0 && nums[mid] < nums[mid - 1]) {
            return nums[mid];
        }

        if (nums[left] <= nums[mid]) {
            // Left half sorted: pivot must be on the right
            left = mid + 1;
        } else {
            // Pivot is in the left half
            right = mid - 1;
        }
    }
};
```

### Why it works

1. **Already sorted range:** if `nums[left] <= nums[right]`, the smallest element in that range is `nums[left]`. Return immediately.
2. **Pivot check:** if `nums[mid] < nums[mid - 1]`, the ascending order breaks exactly at `mid`. That value is the minimum.
3. **Guard `mid > 0`:** prevents checking a nonexistent previous element.
4. **Sorted left half:** `nums[left] <= nums[mid]` means no pivot in that sorted portion. Since the entire current range was *not* sorted (checked above), search right: `left = mid + 1`.
5. **Otherwise:** pivot is on the left: `right = mid - 1`.

### Dry run: `[4, 5, 6, 7, 0, 1, 2]`

| Step | left | right | mid | What happens? |
|---|---:|---:|---:|---|
| 1 | 0 | 6 | 3 | `4 <= 2` false; `7 < 6` false; left sorted → `left = 4` |
| 2 | 4 | 6 | — | `0 <= 2` true → return **0** |

### Another dry run: detect the pivot directly

```text
nums = [3, 4, 5, 1, 2]

left = 0, right = 4, mid = 2 (value 5)
left half [3,4,5] sorted → left = 3

range [1,2] is sorted → return 1
```

The pivot check is still useful on inputs where `mid` lands exactly on the minimum, e.g. `[4,5,1,2,3]`: `mid = 2`, `nums[2] = 1 < nums[1] = 5`, so return 1.

**Time:** O(log n) · **Extra space:** O(1)

---

## Compare my two approaches

| Feature | Approach 1: Min Tracking | Approach 2: Pivot Finding |
|---|---|---|
| Core idea | Remember minimum candidates while discarding halves | Detect pivot or return first element of sorted range |
| Extra variable | `min = Infinity` | None |
| Loop | `start < end` | `left <= right` |
| Key test | `nums[start] <= nums[mid]` | Sorted-range check, pivot check, then sorted-half check |
| Return | `Math.min(min, nums[start])` | `nums[left]` or `nums[mid]` |
| Time | O(log n) | O(log n) |
| Extra space | O(1) | O(1) |

## Important edge cases

| Input | Minimum | Why it matters |
|---|---:|---|
| `[1]` | 1 | Single element |
| `[1, 2, 3]` | 1 | No rotation |
| `[2, 1]` | 1 | Two elements; `left === mid` |
| `[3, 1, 2]` | 1 | Pivot near beginning |
| `[2, 3, 4, 5, 1]` | 1 | Pivot at last index |
| `[-2, -1, -5, -4, -3]` | -5 | Negative values |
| `[4, 5, 6, 7, 0, 1, 2]` | 0 | Typical rotation |

## ⭐ Interview notes

- **Important:** LeetCode 153 guarantees **unique** values. With duplicates (LeetCode 154), these comparisons need different handling.
- In binary search, **loop condition and pointer updates must agree**.
- For minimum tracking, don't forget the **last remaining element**.
- For pivot finding, check whether the **current range is already sorted** before discarding a half.
- Both solutions are optimal: **O(log n) time, O(1) space**.

**Revision task:** Explain why `nums[left] <= nums[mid]` proves the left half is sorted in the distinct-values version, and why a sorted current range lets us immediately return `nums[left]`.
