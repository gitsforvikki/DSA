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

## 4. Binary Tree vs BST

A binary tree restricts the **number of children**, not their values. A BST additionally imposes **ordering**. A balanced BST supports average/typical search in O(log n); a skewed BST can take O(n).

## 5. JavaScript Tree Node

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

## 6. Traversal Basics

- **Preorder DFS:** root → left → right
- **Inorder DFS:** left → root → right (sorted for a valid BST)
- **Postorder DFS:** left → right → root
- **Level-order BFS:** visit level by level, typically with a queue

For the tree `1 → (2, 3)`, preorder = `[1,2,3]`, inorder = `[2,1,3]`, postorder = `[2,3,1]`, level order = `[1,2,3]`.

## 7. ⭐ Tree Corner Cases — Interview Checklist

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

## 8. Quick Base Cases

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

## 9. Learning Roadmap

1. Tree fundamentals, terminology, corner cases
2. DFS preorder, inorder, postorder
3. BFS / level-order traversal
4. Height, depth, diameter, balanced tree
5. BST search, insert, validate
6. Lowest Common Ancestor
7. Practice and revision

## 10. Practice Tracker — Not Yet Solved

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
