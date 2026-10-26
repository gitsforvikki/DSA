# 28. Find the Index of the First Occurrence in a String

**LeetCode:** https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/  
**Difficulty:** Easy  
**Status:** ✅ Solved — Two Pointers (with backtracking) and Fixed-Size Sliding Window

## Problem

Find the first index where the entire `needle` appears **contiguously** inside `haystack`. Return `-1` if it does not occur.

Example: `hay="sadbutsad"`, `need="sad"` → `0`.

> **Important:** Unlike *Is Subsequence*, characters cannot be skipped inside a matching substring.

## Approach 1 — Two Pointers with Backtracking (my first solution)

Use `i` to scan the haystack and `p` to track how many characters of `needle` have matched so far. On a mismatch after a partial match, rewind `i` to retry from the next possible starting index.

```javascript
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(hay, need) {
    let p=0;
    for(let i =0; i < hay.length; i++){
        if(hay[i] == need[p]){
            p++;
            if(p == need.length){
                return i - p + 1;
            }
        }else if( p != 0){
            i = i -p;
            p=0
        }
    }
    return -1;
};
```

### Why the rewind works

Suppose `hay="aaab"`, `need="aab"`:

| Step | i at comparison | p at comparison | Compare | Result |
|---|---:|---:|---|---|
| 1 | 0 | 0 | a = a | p → 1 |
| 2 | 1 | 1 | a = a | p → 2 |
| 3 | 2 | 2 | a ≠ b | i = 2−2 = 0; p = 0 |
| 4 | 1 | 0 | a = a | p → 1 |
| 5 | 2 | 1 | a = a | p → 2 |
| 6 | 3 | 2 | b = b | p → 3; return 3−3+1 = **1** |

The `for` loop's automatic `i++` runs after step 3, so the next comparison starts at index **1**, not 0. This is how we avoid skipping overlapping candidates.

```text
hay:   a a a b
index: 0 1 2 3
         | | |
need:    a a b      → first match starts at index 1
```

**Time:** O(n × m) worst case because characters may be revisited after a mismatch.  
**Extra space:** O(1), just pointers.

## Approach 2 — Fixed-Size Sliding Window (my second solution, recommended for readability)

Treat every possible length-`m` substring as a window. `i` is the start of the window, and `j` compares the window characters to `needle`.

```javascript
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(hay, need) {
    //sliding window approach
    let n = hay.length;
    let m  = need.length;
    for(let i = 0; i <= n-m; i++){
        let j=0;
        //check if needle == window
        for(j = 0; j < m; j++){
            if(hay[i+j] !== need[j]){
                break;
            }
        }
        if(j == m) return i;
    }
    return -1;
};
```

### Why `i <= n - m`?

A length-`m` window must fit inside a length-`n` string. The last possible start is `n-m`; starting later cannot fit the entire needle.

### Sliding window dry run

`hay="aaab"`, `need="aab"`, `m=3`.

| Window start i | Window | Character comparisons | Result |
|---|---|---|---|
| 0 | aaa | a=a, a=a, a≠b | Break, move window |
| 1 | aab | a=a, a=a, b=b | j=3=m → return **1** |

```text
i=0: [a a a] b    ≠ "aab"
i=1:  a [a a b]   = "aab"  ✓
```

**Time:** O((n−m+1) × m), or O(n × m) worst case, because each window may compare up to `m` characters.  
**Extra space:** O(1); no substring copy is created.

> This is a **fixed-size window enumeration with repeated comparison**, not an O(n) rolling-window algorithm. The window moves one position at a time, but matching its contents costs up to O(m).

## Which one should I use?

| Aspect | Two Pointers + Backtracking | Fixed-Size Sliding Window |
|---|---|---|
| Key idea | Scan with i and p; rewind on mismatch | Try each possible window start |
| Worst-case time | O(n × m) | O(n × m) |
| Extra space | O(1) | O(1) |
| Handles overlapping matches | Yes | Yes |
| Readability | Rewind needs explanation | **More straightforward** |
| My recommendation | Good for understanding pointer movement | **Prefer in interviews for this basic approach** |

Both are correct for LeetCode 28's non-empty needle constraint. `===` is generally preferable to `==` in JavaScript, although both work for comparing characters or numeric indices here.

### Advanced note

KMP uses a prefix/LPS table to avoid rechecking earlier characters, achieving **O(n+m)** time and **O(m)** extra space. It is an optional later optimization, not required to understand these two solutions.

## Edge cases

| hay | need | Output | Explanation |
|---|---|---:|---|
| `"hello"` | `"ll"` | 2 | Middle match |
| `"aaab"` | `"aab"` | 1 | Overlapping starts |
| `"sadbutsad"` | `"sad"` | 0 | Return first occurrence |
| `"leetcode"` | `"leeto"` | -1 | No match |
| `"abc"` | `"abc"` | 0 | Whole string matches |
| `"abc"` | `"abcd"` | -1 | Needle longer than haystack |

[← Practice sheet](README.md) · [Dashboard](../DASHBOARD.md)
