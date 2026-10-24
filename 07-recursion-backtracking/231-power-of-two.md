# LeetCode 231 — Power of Two

**Problem:** [Power of Two (LeetCode 231)](https://leetcode.com/problems/power-of-two/)

Given an integer `n`, return `true` if it is a power of two; otherwise return `false`.

**Examples:** `1 → true` (`2⁰`), `16 → true` (`2⁴`), `12 → false`, `0 → false`, `-2 → false`.

## Solution 1 — Iterative (my solution; preferred for O(1) space)

```javascript
/**
 * @param {number} n
 * @return {boolean}
 */
var isPowerOfTwo = function(n) {
    if (n <= 0) return false;

    while (n % 2 == 0) {
        n = n / 2;
    }

    return n == 1;
};
```

**Explanation:**
1. A power of two must be positive, so return `false` for `n <= 0`.
2. While `n` is even, divide it by `2`.
3. If the remaining value is `1`, it was a power of two. Otherwise, it was not.

```text
n = 16: 16 → 8 → 4 → 2 → 1  => true
n = 12: 12 → 6 → 3          => false (3 is odd and not 1)
n = 1:  loop skipped        => true
```

**Time:** `O(log N)` for positive `N`. **Extra space:** `O(1)`.

## Solution 2 — Recursive (my solution, corrected with return)

```javascript
/**
 * @param {number} n
 * @return {boolean}
 */
var isPowerOfTwo = function(n) {
    if (n == 1) return true;
    if (n % 2 != 0 || !Number.isInteger(n) || n <= 0) return false;

    return isPowerOfTwo(n / 2);
};
```

**Explanation:**
1. **True base case:** `n == 1`, because `1 = 2⁰`.
2. **False base case:** A non-positive, odd (other than 1), or non-integer value cannot be reduced to 1 by repeatedly dividing by 2.
3. **Recursive step:** Divide by `2` and **return** the recursive result. The original draft called `isPowerOfTwo(n / 2)` without `return`, which made the outer call return `undefined`.

```text
isPowerOfTwo(8)
  → isPowerOfTwo(4)
      → isPowerOfTwo(2)
          → isPowerOfTwo(1)
              → true
          ← true
      ← true
  ← true

isPowerOfTwo(6)
  → isPowerOfTwo(3)
      → false (odd)
  ← false
```

**Time:** `O(log N)` for positive `N`. **Extra space:** `O(log N)` due to the recursion call stack.

| Approach | Time | Extra space | Key point |
| --- | --- | --- | --- |
| Iterative | O(log N) | **O(1)** | Best space efficiency of these two |
| Recursive | O(log N) | O(log N) | Good recursion practice; remember `return` |

**Note:** LeetCode guarantees an integer input, so `Number.isInteger(n)` is optional there. Both solutions use repeated division, not bit manipulation.


[← Recursion and Backtracking index](./README.md)
