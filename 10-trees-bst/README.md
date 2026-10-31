# 🌳 Trees & Binary Search Trees — Learning Sheet

> **Status:** Fundamentals started · **Solved problems:** 0 (no unverified solves counted)  
> [← DSA Home](../README.md) · [Progress dashboard](../DASHBOARD.md)

## 1. Introduction: What is a Tree?

A **tree** is a hierarchical, connected, acyclic data structure consisting of **nodes** and **edges**. It has one **root**. Every node other than the root has **exactly one parent**; the root has **no parent**. A parent may have multiple children. For `n` nodes, a tree has `n - 1` edges.

```
          A (root)
         /        \
        B          C
       / \          \
      D   E          F
    (leaf nodes: D, E, F)
```

**Example:** B and C have parent A; D and E have parent B; F has parent C. A has no parent. D cannot simultaneously have parents B and C in a tree.

## 2. Essential Terminology

| Term | Meaning | Example above |
|---|---|---|
| Root | Top node, with no parent | A |
| Parent | Node immediately above a child | B is parent of D |
| Child | Node immediately below a parent | D is child of B |
| Siblings | Nodes sharing a parent | D and E |
| Leaf | Node with no children | D, E, F |
| Internal node | Node with at least one child | A, B, C |
| Edge | Connection between parent and child | A–B |
| Ancestor | Parent, grandparent, etc. | A and B are ancestors of D |
| Descendant | Child, grandchild, etc. | D is descendant of A |
| Subtree | A node and all descendants | B, D, E |
| Degree | Number of children | degree(B) = 2 |
| Depth | Number of edges from root to node | depth(D) = 2 |
| Height | Longest downward path in **edges** to a leaf | height(B) = 1; height(A) = 2 |
| Level | Usually depth + 1; some sources start at 0 | A at level 1 |

**Convention:** Here height is measured in edges: leaf height = 0; empty tree height = -1. Some interview problems use **number of nodes** instead (leaf height = 1). Confirm the convention first.

## 3. Types of Trees

- **General tree:** Any number of children per node.
- **Binary tree:** At most two children per node, designated left and right.
- **Full binary tree:** Every node has either 0 or 2 children.
- **Perfect binary tree:** All internal nodes have 2 children and leaves share the same depth.
- **Complete binary tree:** All levels full except possibly the last, which fills left to right.
- **Balanced binary tree:** Height remains approximately logarithmic; exact definition varies (e.g., AVL).
- **Skewed binary tree:** Nodes form a chain; height can reach n - 1.
- **Binary Search Tree (BST):** Every node's left subtree contains smaller values and right subtree contains larger values, under a chosen duplicate policy. This rule applies to **entire subtrees**, not just immediate children.

```
          8
         / \
        3   10
       / \    \
      1   6    14
```

## 4. Binary Tree — Detailed Guide

### 4.1 Definition and rules

A **binary tree** is a rooted tree in which every node has **at most two children**: a **left** child and a **right** child. They are distinct positions, so swapping left and right can change the tree. Values do **not** have to be sorted, unique, or positive.

- A node can have **0, 1, or 2** children; never 3.
- The root has no parent; every other node has exactly one parent.
- Each left/right child is itself the root of a subtree.
- An empty tree (\`root === null\`) is valid.
- Binary tree does **not** mean binary search tree.

\`\`\`text
           10
          /  \
        25    3
       /        \
      7          18

Valid binary tree: 25 > 10 is allowed on the left.
\`\`\`

### 4.2 Left child vs right child matters

\`\`\`text
Tree A             Tree B
   1                  1
  /                    \
 2                      2
\`\`\`

These are **different** binary trees, even though both contain 1 and 2. A missing child must remain \`null\`, not be silently moved to the other side.

### 4.3 Types of binary trees — compare carefully

**Full (strict/proper):** Each node has either **0 or 2** children; never exactly 1.

\`\`\`text
        1
       / \
      2   3
         / \
        4   5
\`\`\`

**Perfect:** All internal nodes have 2 children **and** all leaves are at the same depth.

\`\`\`text
        1
       / \
      2   3
     / \ / \
    4  5 6  7
\`\`\`

**Complete:** All levels except possibly the last are full; nodes on the last level are packed **left to right**.

\`\`\`text
Complete              NOT complete
      1                     1
     / \                   / \
    2   3                 2   3
   / \                   /   /
  4   5                 4   5
\`\`\`

**Balanced:** The height stays small relative to the node count. In an **AVL-style height-balanced** tree, each node's left/right subtree heights differ by at most 1. Different balancing schemes use different definitions.

**Skewed / degenerate:** Every node has only one child, producing a chain.

\`\`\`text
    1
     \
      2
       \
        3
         \
          4
\`\`\`

**Important relationships:** Perfect implies full and complete. Complete does **not** imply full; full does **not** imply complete. A perfect tree is balanced.

### 4.4 Properties and formulas (height measured in edges)

| Property | Formula / explanation |
|---|---|
| Max nodes at depth \`d\` | \`2^d\` |
| Max nodes with height \`h\` | \`2^(h+1) - 1\` |
| Minimum height for \`n >= 1\` nodes | \`ceil(log2(n + 1)) - 1\` |
| Maximum height for \`n >= 1\` nodes | \`n - 1\` (skewed) |
| Edges for \`n >= 1\` nodes | \`n - 1\` |
| Perfect tree node count | \`2^(h+1) - 1\` |
| Perfect tree leaf count | \`2^h\` |
| Full binary tree | \`leaves = nodesWithTwoChildren + 1\` for nonempty trees |

Example: height \`2\` allows at most \`2^3 - 1 = 7\` nodes. **Height and depth are not interchangeable:** depth measures distance *from root*, height measures longest distance *to a leaf*.

### 4.5 Array representation vs linked-node representation

**Linked nodes** use \`{ val, left, right }\`, ideal for arbitrary shapes and LeetCode recursion.

For a **complete binary tree** in a **zero-indexed array**:
- Parent of index \`i > 0\`: \`Math.floor((i - 1) / 2)\`
- Left child index: \`2 * i + 1\`
- Right child index: \`2 * i + 2\`

For sparse trees, LeetCode's level-order arrays use \`null\` placeholders; do **not** assume a trimmed level-order array always obeys the complete-tree index formulas.

### 4.6 Tree traversal, with dry run

\`\`\`text
        1
       / \
      2   3
     / \
    4   5
\`\`\`

| Traversal | Order | Result |
|---|---|---|
| Preorder DFS | Root → Left → Right | \`1,2,4,5,3\` |
| Inorder DFS | Left → Root → Right | \`4,2,5,1,3\` |
| Postorder DFS | Left → Right → Root | \`4,5,2,3,1\` |
| Level-order BFS | Top to bottom, left to right | \`1,2,3,4,5\` |

**Preorder dry run:** visit 1 → enter left subtree at 2 → visit 4 → backtrack → visit 5 → backtrack to 1 → visit 3.

\`\`\`js
function preorder(root, result = []) {
  if (root === null) return result;
  result.push(root.val);
  preorder(root.left, result);
  preorder(root.right, result);
  return result;
}

function inorder(root, result = []) {
  if (root === null) return result;
  inorder(root.left, result);
  result.push(root.val);
  inorder(root.right, result);
  return result;
}

function postorder(root, result = []) {
  if (root === null) return result;
  postorder(root.left, result);
  postorder(root.right, result);
  result.push(root.val);
  return result;
}

function levelOrder(root) {
  if (root === null) return [];
  const queue = [root];
  let head = 0;
  const levels = [];
  while (head < queue.length) {
    const size = queue.length - head;
    const level = [];
    for (let i = 0; i < size; i++) {
      const node = queue[head++];
      level.push(node.val);
      if (node.left !== null) queue.push(node.left);
      if (node.right !== null) queue.push(node.right);
    }
    levels.push(level);
  }
  return levels;
}
\`\`\`

All four traversals visit each node: **O(n) time**. Recursive DFS uses **O(h) auxiliary call-stack space** (plus output); BFS uses **O(w)** queue space where \`w\` is the maximum width (the array-with-head implementation may retain processed entries, so its allocated queue array can reach O(n)).

### 4.7 Binary tree common interview mistakes

- Assuming left value < root < right for every binary tree (only BSTs have ordering).
- Forgetting \`if (root === null)\` in recursive functions.
- Confusing node count with edge count for height/diameter.
- Assuming the tree is balanced, so recursion depth is O(log n).
- Comparing node values instead of node references when values repeat.
- Accidentally losing a subtree while rewiring \`left\` or \`right\`.
- Treating \`0\` or negative values as absent: check \`node !== null\`, not \`if (node.val)\`.

## 5. Binary Search Tree (BST) — Detailed Guide

### 5.1 Definition: the ordering rule applies to entire subtrees

A **BST** is a binary tree with an ordering invariant. With **unique keys**:
- Every value in the **left subtree** is **strictly smaller** than the node's value.
- Every value in the **right subtree** is **strictly greater**.
- **Both subtrees** must also be valid BSTs.

\`\`\`text
           8
          / \
         3   10
        / \    \
       1   6    14
          / \   /
         4   7 13
\`\`\`

Example: 4 < 6 < 8, and 13 > 8 even though 13 is the **left child of 14**. Ordering is relative to **all relevant ancestors**, not just the immediate parent.

**Invalid example:**

\`\`\`text
          10
         /  \
        5    15
            /  \
           6    20

6 < 10 but is in the right subtree of 10 → INVALID.
\`\`\`

### 5.2 BST search — how we eliminate half a direction

To find \`7\` in the BST above:

1. At 8: \`7 < 8\` → go **left** to 3.
2. At 3: \`7 > 3\` → go **right** to 6.
3. At 6: \`7 > 6\` → go **right** to 7.
4. Found! If the next child were \`null\`, the key would be absent.

\`\`\`js
function searchBST(root, target) {
  let curr = root;
  while (curr !== null) {
    if (curr.val === target) return curr;
    curr = target < curr.val ? curr.left : curr.right;
  }
  return null;
}
\`\`\`

**Time:** O(h) = O(log n) for height-balanced BSTs, O(n) for skewed trees. **Extra space:** O(1) iterative.

### 5.3 BST insertion — example and implementation

Insert \`5\` into:

\`\`\`text
Before:                After:
       8                    8
      / \                  / \
     3  10                3  10
      \                    \
       6                    6
                           /
                          5
\`\`\`

Follow comparisons: 5 < 8 → left to 3; 5 > 3 → right to 6; 5 < 6 → left is null → attach new node.

\`\`\`js
function insertIntoBST(root, val) {
  if (root === null) return new TreeNode(val);
  let curr = root;
  while (true) {
    if (val === curr.val) return root; // policy: ignore duplicates
    if (val < curr.val) {
      if (curr.left === null) {
        curr.left = new TreeNode(val);
        return root;
      }
      curr = curr.left;
    } else {
      if (curr.right === null) {
        curr.right = new TreeNode(val);
        return root;
      }
      curr = curr.right;
    }
  }
}
\`\`\`

**Time:** O(h); **extra space:** O(1) iterative. Note this implementation **mutates** the tree and **ignores duplicates**.

### 5.4 BST deletion — three essential cases

1. **Leaf node:** Remove it by setting the parent's link to \`null\`.
2. **One child:** Replace the deleted node with its only child.
3. **Two children:** Replace its value with its **inorder successor** (smallest key in right subtree), then delete that successor; alternatively use the inorder predecessor.

\`\`\`text
Delete 8 (two children):

       8                    10
      / \                  /  \
     3  10      --->      3   12
          \ 
          12

Inorder successor of 8 = 10.
\`\`\`

\`\`\`js
function deleteNode(root, key) {
  if (root === null) return null;
  if (key < root.val) {
    root.left = deleteNode(root.left, key);
  } else if (key > root.val) {
    root.right = deleteNode(root.right, key);
  } else {
    if (root.left === null) return root.right;
    if (root.right === null) return root.left;
    let successor = root.right;
    while (successor.left !== null) successor = successor.left;
    root.val = successor.val;
    root.right = deleteNode(root.right, successor.val);
  }
  return root;
}
\`\`\`

**Time:** O(h); **recursive stack:** O(h). This changes the tree in place. Duplicate policy must be specified for variants allowing duplicates.

### 5.5 BST validation — the important ancestor-bound trap

Checking only \`root.left.val < root.val < root.right.val\` is **not enough**. Each descendant must respect constraints from **every ancestor**.

\`\`\`js
function isValidBST(root) {
  function validate(node, min, max) {
    if (node === null) return true;
    if (node.val <= min || node.val >= max) return false;
    return validate(node.left, min, node.val) &&
           validate(node.right, node.val, max);
  }
  return validate(root, -Infinity, Infinity);
}
\`\`\`

The bounds here assume **finite, distinct numeric keys**. If keys may include infinities or duplicates, adapt the comparison and bound representation. **Time:** O(n); **stack:** O(h).

### 5.6 Why BST inorder traversal is sorted

For each node, inorder visits **all smaller left-subtree values**, then the node, then **all greater right-subtree values**. Therefore a BST with unique keys produces a **strictly increasing** inorder sequence.

For the BST in 5.1: \`[1,3,4,6,7,8,10,13,14]\`.

This helps with **kth smallest**, **range queries**, and **BST validation**.

### 5.7 Minimum, maximum, predecessor, successor

- **Minimum:** Follow \`left\` until \`null\`.
- **Maximum:** Follow \`right\` until \`null\`.
- **Inorder predecessor:** Largest smaller key.
- **Inorder successor:** Smallest greater key.
- If a node has a right subtree, its successor is the minimum of that right subtree. Otherwise walk up ancestors until reaching an ancestor from its left subtree (if parent links exist).

### 5.8 BST complexity comparison

| Operation | Balanced BST | Worst-case skewed BST |
|---|---|---|
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |
| Min / max | O(log n) | O(n) |
| Full traversal | O(n) | O(n) |
| Validate BST | O(n) | O(n) |

A regular BST does **not** automatically rebalance. Inserting sorted values \`1,2,3,4,5\` into an initially empty ordinary BST yields a chain:

\`\`\`text
1
 \
  2
   \
    3
     \
      4
       \
        5
\`\`\`

That is why AVL trees and red-black trees maintain balance using additional rules and rotations.

### 5.9 BST corner cases

- **Empty tree:** search returns \`null\`; insert creates root; delete returns \`null\`; validate returns \`true\`.
- **One node:** delete the root → empty tree.
- **Delete root with 0, 1, or 2 children:** caller must use the **returned root**.
- **Missing key:** search returns \`null\`; deletion leaves tree unchanged.
- **Duplicates:** decide whether to reject, count, or consistently place them; strict validator above rejects duplicates.
- **Deep ancestor violation:** validate against inherited bounds.
- **Sorted insertion:** skewed tree and O(n) operations.
- **Negative/zero values:** normal numeric comparisons still work.
- **Repeated operations:** preserve ordering after each mutation.
- **Node identity vs value:** two nodes with the same value are not the same node.

### 5.10 Binary Tree vs BST — quick revision

| Question | Binary Tree | BST |
|---|---|---|
| Maximum children per node? | 2 | 2 |
| Left and right ordered by value? | Not required | Yes, across entire subtrees |
| Search an arbitrary value | O(n) generally | O(h) |
| Inorder gives sorted values? | Not necessarily | Yes (under BST key ordering) |
| Can become skewed? | Yes | Yes |
| Automatically balanced? | No | No |
| Main uses | Hierarchies, expressions, recursive structures | Ordered lookup, insertion, deletion, range search |

**Interview memory rule:** **Every BST is a binary tree, but not every binary tree is a BST.**


## 6. JavaScript Tree Node

```js
class TreeNode {
  constructor(val, left = null, right = null) {
    this.val = val;
    this.left = left;
    this.right = right;
  }
}

const root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
```

## 7. Traversal Basics

- **Preorder DFS:** root → left → right
- **Inorder DFS:** left → root → right (sorted for a valid BST)
- **Postorder DFS:** left → right → root
- **Level-order BFS:** visit level by level, typically with a queue

For the tree `1 → (2, 3)`, preorder = `[1,2,3]`, inorder = `[2,1,3]`, postorder = `[2,3,1]`, level order = `[1,2,3]`.

## 8. ⭐ Tree Corner Cases — Interview Checklist

| Case | Example / what to verify |
|---|---|
| Empty tree | `root === null`; avoid `root.val` access |
| Single node | Root is also a leaf; depth = 0 |
| Only left child | Right subtree is null |
| Only right child | Left subtree is null |
| Completely left-skewed | Depth O(n); recursion stack risk |
| Completely right-skewed | Same worst-case depth |
| Balanced vs unbalanced | Never assume height O(log n) |
| Missing child in middle | `null` position matters |
| Duplicate values | Define BST duplicate policy |
| Negative / zero values | Don't use truthiness to detect a node |
| Very large / small values | Avoid unsafe fixed BST bounds |
| Root is target | Search and LCA cases |
| Target not present | Clarify whether guaranteed |
| One node is ancestor of another | LCA can equal one target |
| Unequal subtree heights | Diameter/height may cross root or lie within subtree |
| Deep tree | Recursive DFS may overflow JS call stack |
| Repeated values | Compare node identity when problem requires nodes |
| Mutation | Clarify whether modifying links is allowed |
| BST invalid deep descendant | Compare against ancestor-derived bounds |
| Node-count vs edge-count height | Off-by-one trap |
| BFS with `shift()` | Repeated shifting may be costly; use a head index |

### Corner-case diagrams

**Single node**
```
     1   <- root and leaf
```

**Skewed tree**
```
      1
       \
        2
         \
          3
           \
            4
```

**BST trap: immediate children look correct, but deeper descendant violates ancestor rule**
```
           10
          /  \
         5    15
             /  \
            6    20
            ^ INVALID: 6 is in right subtree of 10,
              but 6 < 10
```
The value 6 is in the **right subtree of 10**, so the whole structure is not a valid BST.

## 9. Quick Base Cases

```js
function countNodes(root) {
  if (root === null) return 0;
  return 1 + countNodes(root.left) + countNodes(root.right);
}

function maxDepth(root) { // depth measured in nodes (LeetCode 104)
  if (root === null) return 0;
  return 1 + Math.max(maxDepth(root.left), maxDepth(root.right));
}
```

Both visit each node once: **O(n) time**; recursion stack **O(h)**, where h is tree height (O(n) worst case).

## 10. Learning Roadmap

1. Tree fundamentals, terminology, corner cases
2. DFS preorder, inorder, postorder
3. BFS / level-order traversal
4. Height, depth, diameter, balanced tree
5. BST search, insert, validate
6. Lowest Common Ancestor
7. Practice and revision

## 11. Practice Tracker — Not Yet Solved

- [ ] [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
- [ ] [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)
- [ ] [Same Tree](https://leetcode.com/problems/same-tree/)
- [ ] [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)
- [ ] [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)
- [ ] [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)
- [ ] [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)
- [ ] [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
- [ ] [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> Add each personally solved problem with approach, dry run, diagram, complexity, and mistakes; update the dashboard only after confirming the solve.
