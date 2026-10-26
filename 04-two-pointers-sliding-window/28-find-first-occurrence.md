# 28. Find the Index of the First Occurrence in a String

**LeetCode:** https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/  
**Difficulty:** Easy  
**Status:** ✅ Solved — Three approaches: Two Pointers (with backtracking), Fixed-Size Sliding Window, and KMP

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

## Approach 3 — KMP (Knuth–Morris–Pratt): optimal linear-time solution

**Idea:** Instead of moving the input pointer backwards after a partial match, precompute the **LPS** array for the target. When characters mismatch, LPS tells us how much of the already matched prefix can still be reused.

### What is LPS?

**LPS = Longest Proper Prefix which is also a Suffix.**

- A **prefix** starts at the beginning of a string.
- A **suffix** ends at the end.
- **Proper** means the prefix/suffix cannot be the entire substring.
- `lps[k]` stores the length of the longest proper prefix of `target[0...k]` that is also a suffix of that substring.

Example: `target = "ababaca"`

| Index k | Substring target[0...k] | Longest matching proper prefix/suffix | lps[k] |
|---:|---|---|---:|
| 0 | a | none | 0 |
| 1 | ab | none | 0 |
| 2 | aba | a | 1 |
| 3 | abab | ab | 2 |
| 4 | ababa | aba | 3 |
| 5 | ababac | none | 0 |
| 6 | ababaca | a | 1 |

```text
target: a b a b a c a
index:  0 1 2 3 4 5 6
lps:    0 0 1 2 3 0 1
```

For `"ababa"`, the longest proper prefix and suffix are both `"aba"`, length **3**.

### My third solution (original code)

```javascript
/**
 * @param {string} haystack
 * @param {string} needle
 * @return {number}
 */
var strStr = function(input, target) {
    //KMP
    let n = input.length;
    let m  = target.length;

    let i = 0;
    let j = 1;
    let lps = [0];

    //Creating LPS;
    while(j < m){
        if(target[i] == target[j]){
            lps[j] = i + 1;
            ++j;
            ++i;
        }
        else{
            if(i == 0){
                lps[j] = 0;
                ++j;
            }else{
                i = lps[i-1]
            }
        }
    }

    //now traverse the input and search target
    // pointer--> i for input and pointer j--> for target
    i = j = 0;
    while(i < n){
        if(input[i] == target[j]){
            ++i;
            ++j;
        }else{
            if(j == 0){
                ++i;
            }else{
                j = lps[j-1]
            }
        }
         if(j === m){
            return i - m;
         }
    }

    return -1;
};
```

> LeetCode 28 guarantees a nonempty `needle`. For a reusable function that also accepts an empty target, add `if (m === 0) return 0;` before building the LPS. The code above preserves my submitted approach.

### Phase 1: Build the LPS array

At this stage **both pointers refer to the target**:

- `i`: length of the currently matched proper prefix (also the next prefix character to compare).
- `j`: index of the target character for which we are computing `lps[j]`, initially 1.
- `lps[0] = 0`: a one-character substring has no nonempty proper prefix.

**Case A — characters match:** `target[i] == target[j]`

```javascript
lps[j] = i + 1;
++j;
++i;
```

The matching prefix gets one character longer, so its length is `i+1`. It is **not** `i+j`: `j` is a position, not a matched-length contribution.

**Case B — mismatch and `i === 0`:**

```javascript
lps[j] = 0;
++j;
```

No proper prefix matches this suffix, so record zero and move on.

**Case C — mismatch and `i > 0`:**

```javascript
i = lps[i - 1];
```

Try the next shorter prefix that could still match. **Do not advance `j` yet**; the same target character must be compared against the shorter candidate prefix.

#### LPS construction dry run: `target = "ababaca"`

| j | i before | Compare | Action |
|---:|---:|---|---|
| 1 | 0 | a ≠ b | lps[1] = 0; j = 2 |
| 2 | 0 | a = a | lps[2] = 1; i = 1; j = 3 |
| 3 | 1 | b = b | lps[3] = 2; i = 2; j = 4 |
| 4 | 2 | a = a | lps[4] = 3; i = 3; j = 5 |
| 5 | 3 | b ≠ c | i = lps[2] = 1; **j stays 5** |
| 5 | 1 | b ≠ c | i = lps[0] = 0; **j stays 5** |
| 5 | 0 | a ≠ c | lps[5] = 0; j = 6 |
| 6 | 0 | a = a | lps[6] = 1; finish |

### Phase 2: Search in the input

Reset `i = j = 0`, but now the pointers mean something different:

- `i`: current position in **input/haystack**.
- `j`: current position in **target/needle**, equivalently the count of consecutively matched pattern characters.

**If characters match:** increment both pointers.

**If mismatch and `j === 0`:** advance input pointer `i`; nothing from the target matched yet.

**If mismatch and `j > 0`:** set `j = lps[j - 1]` while leaving `i` unchanged. The already matched target prefix has a suffix that can be reused as a shorter prefix.

**If `j === m`:** all target characters matched; return `i - m`, because `i` points immediately after the match.

#### Search dry run

```text
input:  a b a b a b c
index:  0 1 2 3 4 5 6
target: a b a b c
lps:    0 0 1 2 0
```

| i before | j before | Compare | Action |
|---:|---:|---|---|
| 0 | 0 | a = a | i=1, j=1 |
| 1 | 1 | b = b | i=2, j=2 |
| 2 | 2 | a = a | i=3, j=3 |
| 3 | 3 | b = b | i=4, j=4 |
| 4 | 4 | a ≠ c | j=lps[3]=2; **i remains 4** |
| 4 | 2 | a = a | i=5, j=3 |
| 5 | 3 | b = b | i=6, j=4 |
| 6 | 4 | c = c | i=7, j=5=m; return **7−5=2** |

**Visualization of fallback:**

```text
input:     a b a b a b c
           a b a b c        first candidate fails at 'c'
               a b a b c    reuse the matched "ab" suffix
               ^             next candidate starts at index 2
```

The `input` pointer never goes backward; the LPS value tells us which prefix length is still possible.

### Why KMP is O(n + m)

- **LPS construction:** O(m). Although fallback may happen repeatedly, each increment/fallback changes the matched-prefix length in an amortized linear process.
- **Searching:** O(n). `i` never decreases; `j` can fall back, but total pointer movement is bounded by O(n).
- **Total time:** **O(n + m)**.
- **Extra space:** **O(m)** for the LPS array.

### All three solutions compared

| # | Approach | Worst-case time | Extra space | Main idea |
|---:|---|---|---|---|
| 1 | Two Pointers + Backtracking | O(n × m) | O(1) | Rewind input after partial mismatch |
| 2 | Fixed-Size Sliding Window | O(n × m) | O(1) | Compare every length-m window |
| 3 | **KMP** | **O(n + m)** | O(m) | Reuse partial matches via LPS |

**Interview takeaway:** Sliding Window is easiest to explain; **KMP is asymptotically fastest** for searching a single pattern in a string. All three count as **one solved LeetCode problem**.


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
