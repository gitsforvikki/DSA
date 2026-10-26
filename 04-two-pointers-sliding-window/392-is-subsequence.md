# 392. Is Subsequence

**LeetCode:** https://leetcode.com/problems/is-subsequence/  
**Difficulty:** Easy  
**Status:** ✅ Solved — Two Pointers

## Problem

Given strings `s` and `t`, return `true` if `s` is a subsequence of `t`. A subsequence keeps the **relative order** of characters, but may skip characters in between.

Examples:
- `s = "abc"`, `t = "ahbgdc"` → `true`
- `s = "axc"`, `t = "ahbgdc"` → `false`
- `s = ""`, `t = "abc"` → `true`

## My solution — Two Pointers

```javascript
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var isSubsequence = function(s, t) {
    let p = 0;
    for (let char of t) {
        if (char === s[p]) {
            p++;
        }
    }

    return p === s.length;
};
```

## How it works

- `p` points to the **next character we need to match in `s`**.
- Traverse `t` from left to right, one character at a time.
- When `char === s[p]`, advance `p` because we matched that character in the correct order.
- Otherwise skip that character of `t`; subsequences allow gaps.
- If `p === s.length` at the end, all characters of `s` were matched in order.

**Why no Map or Set?** Presence alone is not enough: `s="abc"`, `t="acb"` has all three characters but wrong order. A `Map<char,index>` also overwrites duplicates, such as `s="aab"`. The two-pointer scan naturally preserves order and handles repeats.

## Dry run

`s="abc"`, `t="ahbgdc"`

| Character from t | Need s[p] | Match? | p after step |
|---|---|---|---:|
| a | a | Yes | 1 |
| h | b | No | 1 |
| b | b | Yes | 2 |
| g | c | No | 2 |
| d | c | No | 2 |
| c | c | Yes | 3 |

Now `p = 3 = s.length`, so return `true`.

```text
s: a     b        c
   |     |        |
t: a  h  b  g  d  c
   ✓     ✓        ✓
```

## Complexity

Let `m = s.length`, `n = t.length`.

- **Time:** O(n) — one traversal of `t`.
- **Extra space:** O(1) — one pointer `p`; no auxiliary collection or constructed string.

## Edge cases

| s | t | Expected | Why |
|---|---|---|---|
| `""` | `"abc"` | true | Empty string is a subsequence |
| `"a"` | `""` | false | Cannot match a nonempty string |
| `"abc"` | `"acb"` | false | Order matters |
| `"aab"` | `"aaab"` | true | Duplicate characters are handled |
| `"abc"` | `"abc"` | true | Exact match |

**Interview takeaway:** When matching a sequence **in order** inside another sequence, a simple forward scan with two pointers is often clearer and more space-efficient than hashing.

[← Two Pointers & Sliding Window sheet](README.md) · [Dashboard](../DASHBOARD.md)
