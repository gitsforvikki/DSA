# 3. Longest Substring Without Repeating Characters

**LeetCode:** https://leetcode.com/problems/longest-substring-without-repeating-characters/  
**Difficulty:** Medium  
**Status:** Solved with guidance (final logic correct after JavaScript declaration fix)  
**Pattern:** Variable-size sliding window + Set

## Problem

Given a string `s`, find the **length** of the longest contiguous substring containing no repeated characters. A substring is contiguous; a subsequence need not be.

Examples: `"abcabcbb" → 3` (`"abc"`), `"bbbbb" → 1`, `"pwwkew" → 3` (`"wke"`), `"" → 0`.

## User's latest original solution (preserved exactly)

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    let set = new Set();
    let maxCount=0;
   
    let i = j = 0
    while(i < s.length){
        if(!set.has(s[i])){
            set.add(s[i]);
            i++;
             maxCount = Math.max(maxCount , i-j);
        }else{
            // delete jth elem before moving j b/c window elem and set element should must be same
            set.delete(s[j]);
            j++;
           
            

        }
    }
    return maxCount;
};
```

**One remaining JavaScript fix:** `let i = j = 0` does not declare `j`. In strict mode this throws a ReferenceError. Replace it with `let i = 0, j = 0;`. The algorithmic logic is otherwise correct.

## Corrected runnable version (only the declaration is changed)

```javascript
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    let set = new Set();
    let maxCount = 0;

    let i = 0, j = 0;

    while (i < s.length) {
        if (!set.has(s[i])) {
            set.add(s[i]);
            i++;
            maxCount = Math.max(maxCount, i - j);
        } else {
            // Keep the Set identical to the current window.
            set.delete(s[j]);
            j++;
        }
    }
    return maxCount;
};
```

## Explanation: what do i, j and Set mean?

- `j` is the left boundary (inclusive) of the current valid window.
- `i` is the index of the **next** character to consider (exclusive right boundary).
- The current window is `s.slice(j, i)`. It contains no duplicates.
- `set` must contain **exactly** the characters in that current window.
- `maxCount` records the longest valid window seen so far.

### When s[i] is not in Set — expand

1. Add `s[i]` to Set.
2. Advance `i`.
3. The new window length is `i-j` (because `i` is exclusive). Update `maxCount`.

### When s[i] is already in Set — shrink

1. Delete `s[j]` from Set.
2. Advance `j`.
3. **Do not advance i.** Retry the same character on the next iteration. Keep shrinking until its previous occurrence has left the window.

This is why `set.delete(s[j])` **must happen before** `j++`. Otherwise Set and the window disagree.

## Pointer diagram: "abba"

```text
indices:    0   1   2   3
characters: a   b   b   a

After reading first two:
            [a   b]  b   a
             j       i       Set={a,b}, max=2

Duplicate b at i=2:
             a  [b]  b   a
                j    i       remove a, then j=1
             a   b  []b   a
                    j/i     remove old b, then j=2

Retry b, add it:
             a   b  [b]  a
                    j       Set={b}, i=3

Read a:
             a   b  [b   a]
                    j        Set={b,a}, max=2
```

## Full dry run: "dvdf"

| Action | j | i (next index) | Set / window | maxCount |
|---|---:|---:|---|---:|
| Start | 0 | 0 | `{}` / `""` | 0 |
| Add `d` | 0 | 1 | `{d}` / `"d"` | 1 |
| Add `v` | 0 | 2 | `{d,v}` / `"dv"` | 2 |
| Duplicate `d`: delete old `d` | 1 | 2 | `{v}` / `"v"` | 2 |
| Retry and add `d` | 1 | 3 | `{v,d}` / `"vd"` | 2 |
| Add `f` | 1 | 4 | `{v,d,f}` / `"vdf"` | **3** |

Answer = **3**. Resetting the whole window would incorrectly lose `"vdf"`.

## Tests and corner cases

| Input | Expected | Reason |
|---|---:|---|
| `""` | 0 | Empty string; loop never runs |
| `"a"` | 1 | Single character |
| `"abc"` | 3 | All distinct; update max on expansion |
| `"bbbbb"` | 1 | Repeated same character |
| `"abba"` | 2 | Duplicate inside window |
| `"dvdf"` | 3 | Must shrink rather than reset |
| `"abcabcbb"` | 3 | Several repeated characters |
| `"pwwkew"` | 3 | Valid substring must be contiguous |

## Complexity

**Time: O(n).** Although the code has a while loop and the left pointer may move multiple times for one right pointer, `i` moves forward at most `n` times and `j` moves forward at most `n` times. Each character is added and removed at most once; average Set operations are O(1). Thus total is O(n), **not O(n²)**.

**Space: O(min(n, k)).** Set holds only characters in the current window, where `k` is the number of distinct possible characters. In the worst case, O(n).

## Mistake tracking and learning history

1. **First attempt:** `set.clear()` and `j=i` on duplicate discarded useful valid characters. Counterexample: `"dvdf"`.
2. **Second attempt:** Moved `i` on a duplicate and did not delete outgoing characters from Set; invariant was broken.
3. **Latest attempt:** Correctly deletes `s[j]`, advances `j`, retries `s[i]`, and updates max after every expansion. **Logical mistakes resolved.**
4. **Still to watch:** `let i = j = 0` does not declare both variables. Use `let i = 0, j = 0;`. This is a recurring JavaScript declaration issue.
5. **Independent retry goal:** Re-solve without looking at the code, explaining why Set always matches `s[j...i-1]`.

### Pre-submit checklist

- Are both pointers declared with `let`?
- Does Set match the active window exactly?
- On a duplicate, do I shrink without skipping the right character?
- Do I update the maximum even when there are no duplicates?
- Have I tested `""`, `"abc"`, `"abba"`, and `"dvdf"`?

[← Practice sheet](README.md) · [Dashboard](../DASHBOARD.md)
