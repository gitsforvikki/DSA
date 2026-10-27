# 424. Longest Repeating Character Replacement

**LeetCode:** https://leetcode.com/problems/longest-repeating-character-replacement/  
**Difficulty:** Medium  
**Status:** Solved with guidance — user wrote the final `if`-based implementation  
**Pattern:** Variable-size sliding window + frequency Map + historical max frequency

## Problem

Given an uppercase English string `s` and an integer `k`, find the maximum length of a substring that can be made of **one repeated character** by replacing at most `k` characters.

Examples: `s="ABAB", k=2 → 4`; `s="AABABBA", k=1 → 4`.

## User's original final solution (preserved)

```javascript
/**
 * @param {string} s
 * @param {number} k
 * @return {number}
 */
var characterReplacement = function(s, k) {
    let map = new Map();
    let maxLength = 0;
    let maxFrequency = 0;
    let i = 0; let j=0;


    while(i < s.length){

        //add the char in map with frequency;
        map.set(s[i] , (map.get(s[i]) ?? 0)  + 1  );
        //update the maxFrquence

        maxFrequency = Math.max(maxFrequency , map.get(s[i]));

        //check for the window validity
        let windowLength = i - j + 1;
        if(windowLength - maxFrequency > k){
            //shrink the window
            map.set(s[j] , map.get(s[j]) -1); //decrease frequency by one
            j++;
        }

        maxLength = Math.max(maxLength , i - j +1);

        //increase window
        i++


    }
    return maxLength;

};
```

## Algorithm

1. Create a frequency `Map`; initialize `i=0` (right pointer), `j=0` (left pointer), `maxFrequency=0`, and `maxLength=0`.
2. Add `s[i]` to the window by incrementing its Map frequency.
3. Update `maxFrequency = Math.max(maxFrequency, map.get(s[i]))`. **Do not decrease it when shrinking.**
4. Calculate `windowLength = i-j+1`.
5. If `windowLength - maxFrequency > k`, decrement the frequency of `s[j]` and increment `j` **once**.
6. Update `maxLength` using the resulting window length, then increment `i`.
7. Return `maxLength`.

## Why frequency, not last-seen index?

LeetCode 3 requires **no duplicate characters**; a last-seen-index Map can jump over duplicates. LeetCode 424 instead allows repeated characters and up to `k` replacements. For a window with a character occurring `f` times, we can replace all other `length-f` characters to match it. Choosing the most frequent character minimizes replacements:

```text
minimum replacements = window length - highest character frequency
```

Example: `"AABA"` has length 4 and highest frequency 3 (`A`), so `4-3=1` replacement is sufficient.

## Pointer diagram and dry run — "AABABBA", k = 1

```text
Index:     0  1  2  3  4  5  6
Chars:     A  A  B  A  B  B  A

At i=3:   [A  A  B  A] B  B  A
           j        i
Length=4, maxFrequency=3, replacements=1 → best=4

At i=4:   [A  A  B  A  B] B  A
           j           i
Length=5, maxFrequency=3, replacements=2 > k
Shrink once (remove A):
            A [A  B  A  B] B  A
              j        i
Length=4; historical maxFrequency still 3
```

| i | Added | Historical maxFrequency | Window length before shrink | Shrink? | j after | Recorded maxLength |
|---:|:---:|---:|---:|:---:|---:|---:|
| 0 | A | 1 | 1 | No | 0 | 1 |
| 1 | A | 2 | 2 | No | 0 | 2 |
| 2 | B | 2 | 3 | No | 0 | 3 |
| 3 | A | 3 | 4 | No | 0 | 4 |
| 4 | B | 3 | 5 | Yes | 1 | 4 |
| 5 | B | 3 | 5 | Yes | 2 | 4 |
| 6 | A | 3 | 5 | Yes | 3 | 4 |

**Answer: 4.** One valid optimal substring is `"AABA"` (replace `B` with `A`).

## Important subtlety: why is a single `if` correct?

This version keeps a **historical, nondecreasing** `maxFrequency`, not necessarily the actual highest frequency of the current window after shrinking. The maintained window can therefore be *temporarily invalid in reality*. The check is a **relaxed validity condition**.

- On each outer iteration, only one character is added; the window grows by at most one.
- `maxFrequency` never decreases. If the relaxed condition fails, shrinking by **one** restores the prior window size, which had already passed that relaxed condition.
- As a result, window length only grows when the relaxed condition allows it; otherwise it stays the same.
- A stale `maxFrequency` cannot produce a new, falsely larger answer: any increase to a new record length must be supported by a sufficiently high frequency that was actually observed in an earlier window. The algorithm returns the correct global optimum, even if some current windows are not truly valid.

**Do not claim that every window after `if` is valid.** If you need the current window to be genuinely valid at every step, recompute the actual maximum frequency when needed and use `while` to shrink until valid (still O(26n) = O(n) for this problem's uppercase alphabet).

### `if` vs `while`

| Version | Max frequency tracking | Shrink | Correct longest length? |
|---|---|---|---|
| User's optimized version | Historical nondecreasing max | Single `if` | Yes |
| Common optimized version | Historical nondecreasing max | `while` | Yes |
| Strictly valid-window version | Recomputed current max | `while` | Yes |

## Complexity

- **Time:** O(n), because each iteration processes one right character, with average O(1) Map operations.
- **Space:** O(1), because the problem limits characters to 26 uppercase English letters (Map has at most 26 keys). For a general alphabet, O(min(n, alphabet size)).

## Edge cases

| Input | k | Output | Why |
|---|---:|---:|---|
| `""` | 1 | 0 | Empty string |
| `"A"` | 0 | 1 | Single character |
| `"AAAA"` | 0 | 4 | Already uniform |
| `"ABAB"` | 2 | 4 | Two replacements |
| `"AABABBA"` | 1 | 4 | One replacement in `"AABA"` |
| `"ABCDE"` | 0 | 1 | No replacement allowed |
| `"ABCDE"` | 4 | 5 | Replace four characters |
| `"BAAAB"` | 2 | 5 | Replace both B characters |

## Mistake tracking / learning notes

1. Initial idea reused the LeetCode 3 **last-seen index Map** and tried to decrement `k` on duplicate detection. That does not capture the objective: make characters equal, not distinct.
2. Corrected to a **frequency Map**, with `windowLength - maxFrequency` measuring replacements needed.
3. Correctly removed `s[j]` from the frequency counts when shrinking, and moved `j` forward.
4. User independently wrote the optimized **single-`if`** version. It is correct because `maxFrequency` is historical and nondecreasing.
5. Interview explanation to remember: stale `maxFrequency` can make the maintained window temporarily invalid; **the maximum-length answer remains correct**.

[← Practice sheet](README.md) · [Dashboard](../DASHBOARD.md)
