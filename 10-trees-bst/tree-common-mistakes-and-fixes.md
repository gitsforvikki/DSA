# 🌳 Tree & BST — 8 Common Mistakes, Fixes and Corner Cases

[← Trees & BST main page](README.md)

> **Revision sheet based on mistakes and improvements from your Tree/BST practice.** The eight patterns include actual bugs and some robustness/readability improvements. For every pattern: why it fails, a corrected JavaScript solution, a dry run, and a checklist. **Do not count this page as a solved problem.**

## Quick index

| # | Mistake pattern | Example problem | Key rule |
|---|---|---|---|
| 1 | Missing recursive `return` | [700 Search in BST](https://leetcode.com/problems/search-in-a-binary-search-tree/) | Return the child's result |
| 2 | Falsy values vs missing values | [230 Kth Smallest](230-kth-smallest-element-in-a-bst.md) | Check `!== null`, not truthiness |
| 3 | Not stopping after child finds answer | [230 Kth Smallest](230-kth-smallest-element-in-a-bst.md) | Recheck after recursive call |
| 4 | Shared state without backtracking | [112 Path Sum](112-path-sum.md) | Pass independent path state or undo mutations |
| 5 | Checking only immediate BST children | [98 Validate BST](https://leetcode.com/problems/validate-binary-search-tree/) | Carry ancestor bounds |
| 6 | Confusing node references and values | [236 LCA](236-lowest-common-ancestor-of-a-binary-tree.md), [235 BST LCA](235-lowest-common-ancestor-of-a-binary-search-tree.md) | Match return type; compare correct types |
| 7 | Wrong traversal direction | [102 Level Order](102-binary-tree-level-order-traversal.md) | Preserve left-to-right order |
| 8 | Confusing local and shared state | [236 LCA](236-lowest-common-ancestor-of-a-binary-tree.md) | Know which frame owns each variable |

---

## 1. Missing `return` in recursion

**Observed in:** Search in BST. You called the recursive function but did not return its result.

### Buggy code

```javascript
function search(curr) {
    if (!curr) return null;
    if (curr.val === val) return curr;
    if (val > curr.val) {
        search(curr.right); // BUG: result discarded
    } else {
        search(curr.left);  // BUG: result discarded
    }
}
```

**Why it fails:** A recursive call is an expression. When the child finds the target and returns a node, its caller must return that node too. Otherwise the parent reaches the end and implicitly returns `undefined`.

### Correct solution

```javascript
var searchBST = function(root, val) {
    function search(curr) {
        if (!curr) return null;
        if (curr.val === val) return curr;

        if (val > curr.val) return search(curr.right);
        return search(curr.left);
    }
    return search(root);
};
```

### Dry run

```text
       4
      / \
     2   7
    / \
   1   3

searchBST(root, 3)
search(4) -> return search(2)
search(2) -> return search(3)
search(3) -> return node(3)
node(3) propagates back through search(2) and search(4).
```

**Corner cases:** target at root; missing target (must return `null`); single-node tree; skewed BST. **Time:** O(h); **space:** O(h) recursion.

**Memory rule:** *A child's return value does not automatically become the parent's return value.*

---

## 2. Confusing falsy values with missing values

**Observed in:** Kth Smallest BST. The check `if(ans)` does not work if the answer is `0`.

### Buggy code

```javascript
let ans = null;
if (ans) return; // 0, false and "" are falsy
```

**Why it fails:** JavaScript treats `0`, `false`, `""`, `null`, `undefined` and `NaN` as falsy. A valid tree node value can be `0`.

### Correct solution

```javascript
if (ans !== null) return;
```

Also distinguish checking the node itself from checking its value:

```javascript
if (!curr) return;       // valid: no TreeNode
if (!curr.val) return;   // WRONG for a TreeNode with value 0
```

### Dry run

```text
      1
     /
    0

k = 1
Visit 0 -> ans = 0
if (ans) -> false!  // incorrectly fails to stop
if (ans !== null) -> true  // correctly stops
```

**Corner cases:** value 0; negative values; answer unset (`null`); false vs undefined if the return type is boolean.

**Memory rule:** *Check presence explicitly; truthiness is not the same as existence.*

---

## 3. Forgetting that parent code continues after a recursive call

**Observed in:** Kth Smallest BST. Even after the left subtree finds the answer, the parent may still decrement `count` and process further nodes.

### Buggy code

```javascript
inorderTraversal(curr.left);
--count;                       // still runs after left child returns
if (count === 0) ans = curr.val;
inorderTraversal(curr.right);
```

### Correct solution (full)

```javascript
var kthSmallest = function(root, k) {
    let ans = null;
    let count = k;

    function inorderTraversal(curr) {
        if (!curr || ans !== null) return;

        inorderTraversal(curr.left);

        // IMPORTANT: the left subtree may already have found k.
        if (ans !== null) return;

        --count;
        if (count === 0) {
            ans = curr.val;
            return;
        }

        inorderTraversal(curr.right);
    }

    inorderTraversal(root);
    return ans;
};
```

### Dry run

```text
       3
      / \
     1   4
      \
       2

k=2 -> inorder visits 1, then 2.
At node 2: count=0, ans=2.
Return to node 1: stop, do not process its remaining code.
Return to node 3: stop, do not decrement count again.
```

**Corner cases:** k=1; k=n; valid answer 0; left subtree already contains kth value. **Time:** O(h+k), worst O(n); **stack:** O(h).

**Memory rule:** *Returning from a child resumes the parent at the next statement, unless the parent explicitly returns.*

---

## 4. Shared path state without backtracking

**Observed in:** Path Sum. A shared outer `totalSum` accumulated across sibling branches because the previous path's contribution was not undone.

### Buggy code

```javascript
let totalSum = 0;
function dfs(curr) {
    if (!curr) return false;
    totalSum += curr.val;
    return dfs(curr.left) || dfs(curr.right);
    // No subtraction when returning from a failed path.
}
```

### Correct solution — pass state as a parameter

```javascript
var hasPathSum = function(root, targetSum) {
    function dfs(curr, sum) {
        if (!curr) return false;
        sum += curr.val;

        if (!curr.left && !curr.right) {
            return sum === targetSum;
        }

        return dfs(curr.left, sum) || dfs(curr.right, sum);
    }

    return dfs(root, 0);
};
```

### Alternative — explicit backtracking with shared state

```javascript
var hasPathSum = function(root, targetSum) {
    let totalSum = 0;

    function dfs(curr) {
        if (!curr) return false;
        totalSum += curr.val;

        let found;
        if (!curr.left && !curr.right) {
            found = totalSum === targetSum;
        } else {
            found = dfs(curr.left) || dfs(curr.right);
        }

        totalSum -= curr.val; // restore before leaving this frame
        return found;
    }

    return dfs(root);
};
```

### Dry run

```text
      5
     / \
    4   8

Target = 13
Left path = 5+4=9 (fails)
Right path must start from 5, not 9.
Correct right path = 5+8=13.
Shared sum without undo: 5+4+8=17 (WRONG).
```

**Corner cases:** empty tree; root-to-leaf requirement; negative node values; single-node tree; two siblings with different sums. **Time:** O(n); **stack:** O(h).

**Memory rule:** *Path state must be independent per branch, or restored on backtrack.*

---

## 5. Checking only immediate children in BST validation

**Observed in:** BST validation discussions. Checking the direct left and right child is not enough; descendants must respect ancestor constraints. Your earlier lower-bound check also used `< min` instead of `<= min`, accidentally allowing equality.

### Invalid BST that passes local child comparisons

```text
         10
        /  \
       5    15
           /  \
          6    20

6 < 15, so it seems locally fine.
But 6 is in the RIGHT subtree of 10, so 6 must be > 10.
```

### Correct solution — min/max constraints

```javascript
var isValidBST = function(root) {
    function validate(curr, min, max) {
        if (!curr) return true;

        // Strict inequalities; equal values are invalid.
        if (curr.val <= min || curr.val >= max) return false;

        return validate(curr.left, min, curr.val) &&
               validate(curr.right, curr.val, max);
    }

    return validate(root, -Infinity, Infinity);
};
```

### Dry run

```text
validate(10, -Infinity, Infinity)
 -> validate(15, 10, Infinity)
    -> validate(6, 10, 15)
       6 <= 10 => INVALID
```

**Corner cases:** duplicate values; deep ancestor violation; negative/zero values; empty tree (valid); skewed BST. The `±Infinity` bound method assumes finite numeric node values. **Time:** O(n); **stack:** O(h).

**Memory rule:** *BST ordering applies to entire subtrees, not only direct children.*

---

## 6. Confusing TreeNode references with numeric values

**Observed in:** LCA 236, LCA BST 235. The required return type is a TreeNode, but one earlier version stored `curr.val`; another compared `p.val > curr` instead of `curr.val`.

### Buggy vs correct

```javascript
ans = curr.val; // WRONG when TreeNode is required
ans = curr;     // CORRECT: stores node reference

if (p.val > curr) { /* WRONG: number vs object */ }
if (p.val > curr.val) { /* CORRECT: number vs number */ }
```

For general trees, node identity is safest:

```javascript
if (curr === p || curr === q) {
    count++;
}
```

### Correct solution — BST LCA

```javascript
var lowestCommonAncestor = function(root, p, q) {
    let ans = null;
    function findLCA(curr) {
        if (ans || !curr) return;

        if (p.val < curr.val && q.val < curr.val) {
            findLCA(curr.left);
        } else if (p.val > curr.val && q.val > curr.val) {
            findLCA(curr.right);
        } else {
            ans = curr;
        }
    }
    findLCA(root);
    return ans;
};
```

### Dry run

```text
       6
      / \
     2   8
    / \
   0   4

p = node(2), q = node(4)
At 6: both smaller -> go left.
At 2: p equals current -> ans = node(2), not number 2.
```

**Corner cases:** target equals current ancestor; nodes with same value in general trees; null node; return type number vs TreeNode. **Time BST LCA:** O(h); **stack:** O(h).

**Memory rule:** *`curr` is a node; `curr.val` is its value. Always inspect the function's return type.*

---

## 7. Traversal direction and output ordering

**Observed in:** Level Order using recursive DFS. Traversing right before left can still group nodes by level, but produces the wrong left-to-right order within a level.

### Buggy code

```javascript
dfs(curr.right, level + 1);
dfs(curr.left, level + 1);
```

### Correct solution — DFS by level, left first

```javascript
var levelOrder = function(root) {
    const ans = [];

    function dfs(curr, level) {
        if (!curr) return;

        if (!ans[level]) ans[level] = [];
        ans[level].push(curr.val);

        dfs(curr.left, level + 1);
        dfs(curr.right, level + 1);
    }

    dfs(root, 0);
    return ans;
};
```

### Dry run

```text
       1
      / \
     2   3
    / \ / \
   4  5 6  7

Left-first DFS -> [[1], [2,3], [4,5,6,7]]
Right-first DFS -> [[1], [3,2], [7,6,5,4]]
```

**Corner cases:** single child; uneven depths; missing children; empty tree; levels with multiple nodes. **Time:** O(n); **stack:** O(h), excluding output.

**Memory rule:** *Grouping by level does not automatically preserve the required order.*

---

## 8. Confusing local state, parameters, and shared closure state

**Observed in:** Bottom-Up DFS LCA counting. This is primarily a recursion mental-model lesson, not necessarily a bug in your final solution.

### Important distinction

```javascript
let ans = null;          // ONE shared variable in closure

function findLCA(curr) {
    let count = 0;       // NEW local variable per recursive call
}
```

Every invocation gets its own `count`. Child counts are transferred to the parent **through return values**, not because all frames share one count.

### Correct solution — bottom-up subtree counts

```javascript
var lowestCommonAncestor = function(root, p, q) {
    let ans = null;

    function findLCA(curr) {
        if (!curr) return 0;

        const leftCount = findLCA(curr.left);
        const rightCount = findLCA(curr.right);

        let count = leftCount + rightCount;
        if (curr === p || curr === q) count++;

        if (count === 2 && ans === null) ans = curr;

        return count;
    }

    findLCA(root);
    return ans;
};
```

### Call stack dry run

```text
        3
       / \
      5   1
       \
        4

p=5, q=4

findLCA(4): local count = 1 -> returns 1
findLCA(5): local count = 0 initially
    leftCount = 0, rightCount = 1
    self matches p -> count = 2
    shared ans = node(5)
    returns 2
findLCA(3): local count = 0 initially
    leftCount = 2, rightCount = 0
    count = 2; ans already set -> remains node(5)
```

### Variable ownership table

| Variable | Scope | Shared between recursive frames? | Purpose |
|---|---|---|---|
| `curr` | Parameter | No | Current node |
| `leftCount` | Local | No | Left child's returned count |
| `rightCount` | Local | No | Right child's returned count |
| `count` | Local | No | Current subtree's count |
| `ans` | Outer closure | **Yes** | Stores final LCA |

**Corner cases:** p is ancestor of q; targets in different subtrees; root is LCA; deep skewed tree. **Time:** O(n); **stack:** O(h). LeetCode 236 guarantees two distinct targets exist.

**Memory rule:** *Local variables belong to one stack frame; shared closure variables are visible to all frames.*

---

## Final pre-submit checklist — 12 questions

- [ ] Did I handle `root === null` correctly?
- [ ] Does my helper return the **right type** (node, number, boolean, array)?
- [ ] Am I returning recursive results when the caller needs them?
- [ ] If a child found the answer, does the parent stop when required?
- [ ] Did I avoid `if(ans)` when 0 or false can be valid?
- [ ] Does shared mutable state need backtracking?
- [ ] Are local variables and shared closure variables clearly distinguished?
- [ ] Did I preserve left/right order for traversal output?
- [ ] Did I check ancestor bounds for BST validation?
- [ ] Am I comparing `curr` to a node and `curr.val` to a value?
- [ ] Did I test single-node, skewed, zero/negative and problem-specific boundary cases?
- [ ] Did I analyze O(h) recursion stack and O(n) skewed worst case?

### Recommended practice

Before submitting any tree problem, run **one ordinary case** and **one deliberately tricky case**. Write down what each recursive call **receives**, **returns**, and **modifies**. This catches most return-flow, early-stop and shared-state mistakes.

[← Back to Trees & BST main page](README.md)
