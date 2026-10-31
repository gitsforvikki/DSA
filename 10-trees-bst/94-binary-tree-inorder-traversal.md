# 94. Binary Tree Inorder Traversal — Recursive DFS

**Status:** ✅ Solved independently · **Difficulty:** Easy · **Pattern:** DFS / recursion  
[LeetCode 94](https://leetcode.com/problems/binary-tree-inorder-traversal/) · [← Trees & BST](README.md)

## 1. Problem and core idea

Given the root of a binary tree, return node values in **inorder: Left → Root → Right**.

Unlike preorder (Root → Left → Right), inorder visits the **entire left subtree before adding the current node**. Then it visits the right subtree.

## 2. Your original JavaScript solution

```js
var inorderTraversal = function(root) {
    let ans = [];
    var treeTraversal = function(curr){
        if(!curr) return;
        treeTraversal(curr.left);
        ans.push(curr.val);
        treeTraversal(curr.right);
    }
    treeTraversal(root);
    return ans;
};
```

The helper uses the outer `ans` array through a **closure**. Every recursive invocation contributes to that same array. `if (!curr) return;` is the base case.

## 3. Visual example — follow the left-first rule

```text
                  A
                /   \
               B     C
              /     / \
             D     E   F
                    \
                     G
```

**Expected inorder:** `[D, B, A, E, G, C, F]`

Compare preorder on the same tree: `[A, B, D, C, E, G, F]`. Only the position of `ans.push(curr.val)` changes!

## 4. Complete recursion flow — calls, returns and output

```text
treeTraversal(A)
│
├─ LEFT: treeTraversal(B)              (A is PAUSED)
│  ├─ LEFT: treeTraversal(D)           (B is PAUSED)
│  │  ├─ LEFT: null → return
│  │  ├─ PUSH D                        ans = [D]
│  │  └─ RIGHT: null → return
│  │     ↳ D returns to B
│  ├─ PUSH B                           ans = [D,B]
│  └─ RIGHT: null → return
│     ↳ B returns to A
├─ PUSH A                              ans = [D,B,A]
└─ RIGHT: treeTraversal(C)             (A is PAUSED)
   ├─ LEFT: treeTraversal(E)           (C is PAUSED)
   │  ├─ LEFT: null → return
   │  ├─ PUSH E                        ans = [D,B,A,E]
   │  └─ RIGHT: treeTraversal(G)       (E is PAUSED)
   │     ├─ LEFT: null → return
   │     ├─ PUSH G                     ans = [D,B,A,E,G]
   │     └─ RIGHT: null → return
   │        ↳ G returns to E; E returns to C
   ├─ PUSH C                           ans = [D,B,A,E,G,C]
   └─ RIGHT: treeTraversal(F)          (C is PAUSED)
      ├─ LEFT: null → return
      ├─ PUSH F                        ans = [D,B,A,E,G,C,F]
      └─ RIGHT: null → return
         ↳ F returns to C; C returns to A

A returns → final ans = [D,B,A,E,G,C,F]
```

**Key insight:** When `treeTraversal(curr.left)` is called, the current function **pauses**. Once that left call returns, it **resumes on the next line**, `ans.push(curr.val)`. After pushing, it calls the right subtree. A `return` in a null-child invocation only ends **that invocation**, not the entire traversal.

## 5. Recursion call stack — snapshot at D

```text
TOP ┌──────────────────────┐
    │ treeTraversal(D)     │ ← running; its left call returned
    ├──────────────────────┤
    │ treeTraversal(B)     │ ← waiting for left call to finish
    ├──────────────────────┤
    │ treeTraversal(A)     │ ← waiting for left call to finish
    └──────────────────────┘
BOTTOM
```

Once D's left child returns `null`, D gets pushed. D's right child also returns `null`. D's frame ends, and **B resumes at `ans.push(B)`**. Later, B returns and **A resumes at `ans.push(A)`**.

## 6. Step-by-step dry run

| Step | Event | `ans` |
|---:|---|---|
| 1 | A → B → D → D.left is null | `[]` |
| 2 | Push D; D.right null → return | `[D]` |
| 3 | Resume B, push B; B.right null | `[D,B]` |
| 4 | Resume A, push A | `[D,B,A]` |
| 5 | A.right → C.left → E.left null; push E | `[D,B,A,E]` |
| 6 | E.right → G.left null; push G; G.right null | `[D,B,A,E,G]` |
| 7 | Resume C, push C | `[D,B,A,E,G,C]` |
| 8 | C.right → F.left null; push F; F.right null | `[D,B,A,E,G,C,F]` |

## 7. Why the solution is correct

For any non-null node, the function (1) recursively processes **all** nodes in its left subtree, (2) appends the node's value exactly once, and (3) processes **all** nodes in its right subtree. The null base case terminates recursion. By applying the same logic to every subtree, the final array is exactly inorder.

## 8. Corner cases

| Input | Output | Reason |
|---|---|---|
| `root = null` | `[]` | Base case |
| Single node `[1]` | `[1]` | Left null, push 1, right null |
| Only left chain `1 → 2 → 3` | `[3,2,1]` | Descend fully left before pushing |
| Only right chain `1 → 2 → 3` | `[1,2,3]` | Push each node before moving right |
| `[1,null,2,3]` | `[1,3,2]` | 3 is left child of 2 |
| Repeated or zero values | Preserved | Check `curr` reference, not `curr.val` |

## 9. Complexity

- **Time: O(n)** — each node visited once.
- **Auxiliary space: O(h)** — recursion stack, where `h` is tree height in nodes. Worst case O(n) for a skewed tree; O(log n) for a height-balanced tree.
- **Output space: O(n)** — answer array, not counted as auxiliary stack.

## 10. Interview insights

- **Preorder:** push → left → right.
- **Inorder:** left → push → right.
- **Postorder:** left → right → push.
- A **valid BST with distinct values** produces sorted ascending values via inorder. A general binary tree does **not** guarantee sorted output.
- `if (!curr) return;` is safe because `curr` is an object or `null`, even if `curr.val === 0`.
- Very deep trees can overflow the JavaScript call stack; an iterative stack-based solution avoids recursive depth limits.

**Revision task:** Dry-run `[4,2,6,1,3,5,7]` and predict inorder without executing code. Answer: `[1,2,3,4,5,6,7]`.

## 11. Solution 2 — Iterative Inorder Traversal (Explicit Stack)

**Status:** ✅ Solved by you · **Order:** Left → Root → Right

### Your original JavaScript code

```js
var inorderTraversal = function(root) {
    let stack =[];
    let ans = [];
    if(!root) return ans;

    let curr = root;
    while( stack.length || curr){

        //this while loop push all the left most nodes in stack
        while(curr){
            stack.push(curr);
            curr = curr.left;
        }
        curr = stack.pop();
        ans.push(curr.val);
        curr = curr.right;

    }
    return ans;
};
```

### Why are there two while loops?

- **Outer `while (stack.length || curr)`:** Continue while a node is available to explore **or** an ancestor is waiting on the stack. Both must be empty/null before traversal finishes.
- **Inner `while (curr)`:** Keep going to the **leftmost** node, pushing each ancestor onto the stack. Nothing is appended yet because inorder must visit the left subtree first.
- **`curr = stack.pop()`:** Retrieve the next node whose left subtree is finished.
- **`ans.push(curr.val)`:** Visit that node (the **Root** part of Left → Root → Right).
- **`curr = curr.right`:** Explore the right subtree next. On the following iteration, the inner loop again descends left within that right subtree.

### Visual: how the explicit stack replaces recursion

```text
           1
          / \
         2   3
        / \
       4   5

Start: curr = 1, stack = []

Descend LEFT:
  push 1 → stack [1]       curr = 2
  push 2 → stack [1,2]     curr = 4
  push 4 → stack [1,2,4]   curr = null

Now pop 4, visit 4.       ans = [4]
4.right = null, so pop 2, visit 2.
Then curr = 2.right = 5, push 5, pop 5.
Next pop 1, visit 1; then go right to 3.

Final inorder = [4,2,5,1,3]
```

**Stack is shown bottom → top; the rightmost entry is popped first.**

### Complete dry run

| Step | Action | Stack after action | `curr` next | `ans` |
|---:|---|---|---|---|
| 1 | Push 1, 2, 4 while moving left | `[1,2,4]` | `null` | `[]` |
| 2 | Pop and visit 4 | `[1,2]` | `null` | `[4]` |
| 3 | Pop and visit 2 | `[1]` | `5` | `[4,2]` |
| 4 | Push 5 while moving left | `[1,5]` | `null` | `[4,2]` |
| 5 | Pop and visit 5 | `[1]` | `null` | `[4,2,5]` |
| 6 | Pop and visit 1 | `[]` | `3` | `[4,2,5,1]` |
| 7 | Push 3 while moving left | `[3]` | `null` | `[4,2,5,1]` |
| 8 | Pop and visit 3 | `[]` | `null` | `[4,2,5,1,3]` |
| 9 | Outer loop ends: no `curr` and empty stack | `[]` | `null` | `[4,2,5,1,3]` |

### Why `stack.length || curr` (OR), not AND?

Consider when `curr === null` after reaching the leftmost node, but the stack contains `[1,2,4]`. We **must continue** to pop and visit those nodes. Using `&&` would stop too early.

Conversely, after popping a node, its `right` child may be non-null even when the stack is empty. We must continue exploring that subtree.

### Complexity comparison

| Metric | Solution 1: Recursive | Solution 2: Iterative |
|---|---|---|
| Time | O(n) | O(n) |
| Auxiliary space | O(h) call stack | O(h) explicit stack |
| Worst-case space | O(n) skewed tree | O(n) skewed tree |
| Output | O(n) | O(n) |
| Deep recursion overflow | Possible | Avoided |

Here `h` is the tree height in nodes.

### Corner cases and interview pitfalls

- **Empty tree:** `if(!root) return ans;` returns `[]` safely. The outer loop alone would also handle null root.
- **Single node:** Push, pop, visit; returns `[root.val]`.
- **Only left children:** Push all nodes; pop in reverse depth order.
- **Only right children:** Each node is visited, then `curr` moves right.
- **Duplicates and zero values:** All node values are included.
- **Do not append values inside the inner while loop:** That would visit nodes before their left subtree and break inorder.
- **Do not forget `curr = curr.right`:** Otherwise right subtrees are skipped.

**Important:** This is a second approach to the same LeetCode 94 problem; the dashboard solved-problem count does **not** increase.

---

[← Trees & BST learning sheet](README.md)
