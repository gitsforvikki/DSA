# 144. Binary Tree Preorder Traversal — Recursive DFS

**Status:** ✅ Solved independently · **Pattern:** DFS + recursion · **Difficulty:** Easy  
[LeetCode 144](https://leetcode.com/problems/binary-tree-preorder-traversal/) · [← Tree learning sheet](README.md)

## 1. Problem

Given the root of a binary tree, return the values in **preorder**: **Root → Left → Right**.

Your solution uses an outer answer array and a nested recursive helper. We preserve it exactly.

## 2. Your JavaScript solution

```js
var preorderTraversal = function(root) {
    let ans = [];
    var treeTraversal = function(curr){
        if(!curr) return;
        ans.push(curr.val);
        treeTraversal(curr.left);
        treeTraversal(curr.right);
    }
    treeTraversal(root);

    return ans;
};
```

**Why this works:** Every non-null node is appended **before** exploring its left and right children. The recursive calls visit the entire left subtree before the right subtree. `if (!curr) return;` stops recursion at missing children.

## 3. Tree used in the visual explanation

The same node layout as your reference screenshot:

```text
                 A
               /   \
              B     C
             /     / \
            D     E   F
                   \
                    G
```

Expected result: **[A, B, D, C, E, G, F]**

> These letters represent node values for visualization. The JavaScript code works with numbers or other values too.

## 4. Recursion flow diagram — calls, nulls, and returns

The following uses a **text diagram**, not Mermaid, so GitHub can display it even when rich diagram rendering fails.

```text
treeTraversal(A)
│
├─ push A                              ans = [A]
├─ LEFT: treeTraversal(B)
│  ├─ push B                            ans = [A,B]
│  ├─ LEFT: treeTraversal(D)
│  │  ├─ push D                          ans = [A,B,D]
│  │  ├─ LEFT:  treeTraversal(null) → return
│  │  └─ RIGHT: treeTraversal(null) → return
│  │     ↳ return from D
│  └─ RIGHT: treeTraversal(null) → return
│     ↳ return from B
└─ RIGHT: treeTraversal(C)
   ├─ push C                            ans = [A,B,D,C]
   ├─ LEFT: treeTraversal(E)
   │  ├─ push E                          ans = [A,B,D,C,E]
   │  ├─ LEFT: treeTraversal(null) → return
   │  └─ RIGHT: treeTraversal(G)
   │     ├─ push G                       ans = [A,B,D,C,E,G]
   │     ├─ LEFT:  treeTraversal(null) → return
   │     └─ RIGHT: treeTraversal(null) → return
   │        ↳ return from G
   │     ↳ return from E
   └─ RIGHT: treeTraversal(F)
      ├─ push F                          ans = [A,B,D,C,E,G,F]
      ├─ LEFT:  treeTraversal(null) → return
      └─ RIGHT: treeTraversal(null) → return
         ↳ return from F
      ↳ return from C
↳ return from A

preorderTraversal returns ans
```

### Key observation: **recursion pauses and resumes**

When execution reaches `treeTraversal(curr.left)`, the current function **pauses** at that line. After the left recursive call **returns**, the current function **resumes at the next line**, `treeTraversal(curr.right)`. It does not start from the top again.

```text
Inside treeTraversal(B):

ans.push(B)
treeTraversal(B.left)   ───> treeTraversal(D)
                              push D
                              left(null)  → return
                              right(null) → return
                         <─── D finished
treeTraversal(B.right)  ───> null → return
                         <─── B finished; resume A's right call
```

This is why **D is processed before C**, and **G before F**.

## 5. Step-by-step execution table

| Step | Call / event | Change to ans |
|---:|---|---|
| 1 | Visit A | [A] |
| 2 | A.left → visit B | [A, B] |
| 3 | B.left → visit D | [A, B, D] |
| 4 | D.left is null → return | No change |
| 5 | D.right is null → return; D finishes | No change |
| 6 | B.right is null → return; B finishes | No change |
| 7 | Resume A; A.right → visit C | [A, B, D, C] |
| 8 | C.left → visit E | [A, B, D, C, E] |
| 9 | E.left is null → return | No change |
| 10 | E.right → visit G | [A, B, D, C, E, G] |
| 11 | G.left and G.right are null → return twice | No change |
| 12 | Resume C; C.right → visit F | [A, B, D, C, E, G, F] |
| 13 | F.left and F.right are null → return twice | No change |
| 14 | Return to A, then return final ans | [A, B, D, C, E, G, F] |

## 6. Call-stack mental model

**Going down (calls):** New function frames are pushed onto the stack.

```text
At D:
┌──────────────────┐
│ treeTraversal(D) │  ← currently executing
├──────────────────┤
│ treeTraversal(B) │  ← paused at its left call
├──────────────────┤
│ treeTraversal(A) │  ← paused at its left call
└──────────────────┘
```

**Coming back (returns):** D's null children return, then D finishes; B resumes and explores its right child. After B finishes, A resumes and explores C.

**Important:** The `ans` array is **shared** by every call because the nested function closes over the outer variable. There is **one answer array**, not a new array per recursive call.

## 7. Base case and corner cases

| Input | Output | Why |
|---|---|---|
| `root = null` | `[]` | Helper returns immediately |
| One node `[1]` | `[1]` | Push once; both children null |
| Only left chain `1 → 2 → 3` | `[1,2,3]` | Visit before descending |
| Only right chain `1 → 2 → 3` | `[1,2,3]` | Left null, then right |
| Repeated values | All occurrences included | Nodes visited, not deduplicated |
| Node value `0` | Includes `0` | `!curr` checks the node reference, not its value |

## 8. Complexity

- **Time: O(n)** — each real node is visited exactly once.
- **Auxiliary space: O(h)** — recursion call stack, where `h` is tree height in nodes.
- **Worst-case auxiliary space: O(n)** for a skewed tree.
- **Output space: O(n)** for `ans` (separate from auxiliary stack).

## 9. Interview notes and mistakes to avoid

1. **Preorder = Root, Left, Right**. Changing push position changes the traversal: between calls = inorder; after calls = postorder.
2. `return` from a null call returns **only that recursive invocation**, not the whole algorithm.
3. After a left call completes, execution resumes at the **right call of the same parent frame**.
4. `ans` is shared via closure; no need to return arrays from every helper invocation.
5. The empty-tree check is essential. A very deep tree may overflow JavaScript's recursion stack; an iterative stack-based traversal is an alternative.

**Practice prompt:** Draw the call tree and track `ans` for `[1,null,2,3]`. Expected preorder: `[1,2,3]`.

## 10. Solution 2 — Iterative Preorder Traversal (Stack)

**Status:** ✅ Corrected and solved by you · **Pattern:** Explicit stack + LIFO  
**Traversal order:** Root → Left → Right

### Your original iterative JavaScript solution

```js
var preorderTraversal = function(root) {
  //Iterative approach
  let ans = [];
  let stack = [];
  if(!root) return ans;

  stack.push(root);
  while(stack.length){
    let curr = stack.pop();
    ans.push(curr.val);
    if(curr.right) stack.push(curr.right);
    if(curr.left) stack.push(curr.left);

  }  
  return ans;
};
```

### Why do we push the right child before the left child?

A stack follows **LIFO — Last In, First Out**. We want **Root → Left → Right**, so after visiting the root, push **right first** and **left second**. The left child is now on top of the stack and will be popped first.

```text
           1
          / \
         2   3
        / \
       4   5

After visiting 1:
      TOP
      ┌───┐
      │ 2 │ ← pushed last, popped first
      ├───┤
      │ 3 │ ← pushed first, processed later
      └───┘
    BOTTOM

Visit order: 1 → 2 → 4 → 5 → 3
```

### Detailed iterative dry run

The stack is shown **bottom → top** (rightmost item is popped next).

| Iteration | Pop | Action | Stack after pushes | Answer |
|---:|---|---|---|---|
| Start | — | Push root 1 | `[1]` | `[]` |
| 1 | 1 | Add 1; push 3, then 2 | `[3, 2]` | `[1]` |
| 2 | 2 | Add 2; push 5, then 4 | `[3, 5, 4]` | `[1,2]` |
| 3 | 4 | Add 4; no children | `[3, 5]` | `[1,2,4]` |
| 4 | 5 | Add 5; no children | `[3]` | `[1,2,4,5]` |
| 5 | 3 | Add 3; no children | `[]` | `[1,2,4,5,3]` |

**Final output:** `[1,2,4,5,3]`.

### Step-by-step algorithm

1. Create `ans = []` and `stack = []`.
2. **If `root` is null, return `[]` immediately.** This avoids reading `curr.right` from a null node.
3. Push `root` onto the stack.
4. While the stack is not empty: pop the top node, add its value to `ans`, push its **right child**, then its **left child** (if present).
5. When the stack is empty, every node has been processed; return `ans`.

### Complexity — iterative vs recursive

| Metric | Recursive DFS (Solution 1) | Iterative stack (Solution 2) |
|---|---|---|
| Time | O(n) | O(n) |
| Auxiliary space | O(h) call stack | O(h) explicit stack for binary-tree preorder |
| Worst case | O(n) | O(n) |
| Output array | O(n) | O(n) |
| Deep recursion overflow | Possible | Avoided |

Here `h` is tree height in nodes. For this iterative preorder algorithm, the stack holds at most O(h) pending nodes because each level contributes at most one unvisited sibling while following a path.

### Corner cases

| Input | Result | Why |
|---|---|---|
| `root = null` | `[]` | Early return before `stack.push(root)` |
| Single node `[1]` | `[1]` | Pop once; no children to push |
| Only left children | Root-to-leaf order | Only left nodes are pushed |
| Only right children | Root-to-leaf order | Only right nodes are pushed |
| Node value `0` | Includes 0 | Checks child node references, not child values |
| Duplicate values | Keeps duplicates | Visits nodes, not unique values |

### Interview mistakes to remember

- **Do not push left before right** for this stack-based preorder: that would pop right first and break the traversal order.
- **Do not push a null root** and then read `curr.right` / `curr.left` without checking; your early `if(!root) return ans;` fixes this.
- This is **another approach to the same LeetCode 144 problem**, not a new solved problem for the dashboard.

---

[← Back to Trees & BST](README.md)
