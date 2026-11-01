# 112. Path Sum — Four DFS Approaches

[← Tree sheet](README.md) · [LeetCode 112](https://leetcode.com/problems/path-sum/)

**Difficulty:** Easy · **Status:** Four user-provided correct solutions · **Pattern:** Recursive DFS

## Problem and example

Return true when at least one **root-to-leaf** path sums to targetSum. A leaf has **no children**. Checking an internal node is not enough.

~~~text
         5
        / \
       4   8
      /   / \
     11  13  4
    /  \
   7    2

Target 22: 5 → 4 → 11 → 2 = 22 (true)
~~~

## Approach 1 — Running sum + logical OR (top-down DFS)

Your solution:

~~~js
var hasPathSum = function(root, targetSum) {
    function traverseSum(curr , sum){
        if(!curr) return false;
        sum += curr.val;
        //check the sum and targetSum at the leaf node
        if(!curr.left && !curr.right){
            return sum === targetSum
        }
        return traverseSum(curr.left , sum) || traverseSum(curr.right , sum);
    }
    return traverseSum(root , 0)
};
~~~

**Explanation:** Carry the accumulated sum down the tree. At a leaf compare the complete path sum with the target. The OR operator means either left or right subtree can contain a matching path. JavaScript short-circuits: if left returns true, right is skipped.

### Recursive call tree and boolean return flow

~~~text
Tree:         5
             / \
            4   8
           / \
          7   2
Target: 11

traverseSum(5, 0)   sum becomes 5
└─ traverseSum(4, 5)  sum becomes 9
   ├─ traverseSum(7, 9) → sum 16 → false
   └─ traverseSum(2, 9) → sum 11 → true
      ↑ false || true = true (at node 4)
      ↑ true returns to node 5
      ↑ right subtree 8 is SKIPPED
Final: true
~~~

~~~mermaid
flowchart TD
    A[DFS node and sum] --> B{Node null?}
    B -- Yes --> C[Return false]
    B -- No --> D[Add node value to sum]
    D --> E{Leaf?}
    E -- Yes --> F[Return sum equals target]
    E -- No --> G[DFS left]
    G --> H{Left true?}
    H -- Yes --> I[Return true; skip right]
    H -- No --> J[DFS right and return its result]
~~~

**Time:** O(n) worst case. **Stack:** O(h).

## Approach 2 — Running sum + explicit left and right boolean returns

Your solution (both recursive calls happen before the checks):

~~~js
var hasPathSum = function(root, targetSum) {
    function traverseSum(curr , sum){
        if(!curr) return false;
        sum += curr.val;
        //check the sum and targetSum at the leaf node
        if(!curr.left && !curr.right){
            return sum === targetSum
        }
         let leftResult = traverseSum(curr.left , sum)
         let rightResult = traverseSum(curr.right , sum);
         if(leftResult === true){
            return true;
         }
         if(rightResult === true){
            return true;
         }
         return false;
    }
    return traverseSum(root , 0)
};
~~~

**Explanation:** Left and right subtree each return true/false. Parent checks both values explicitly. Unlike Approach 1, the original code evaluates **both** recursive calls even when left already returned true.

### Return trace

~~~text
Target 11, tree above:

Node 4:
  traverseSum(7,9) → false
  traverseSum(2,9) → true
  leftResult=false, rightResult=true → return true

Node 5:
  leftResult=true
  traverseSum(8,5) → false   [still runs!]
  rightResult=false
  if(leftResult === true) → return true
~~~

To keep the explicit style but short-circuit, check the left result **before** making the right recursive call:

~~~js
let leftResult = traverseSum(curr.left, sum);
if (leftResult) return true;
let rightResult = traverseSum(curr.right, sum);
if (rightResult) return true;
return false;
~~~

This is an optimization of Approach 2, not an additional counted approach.

**Time:** O(n). **Stack:** O(h).

## Approach 3 — Running sum + shared closure variable

Your solution:

~~~js
var hasPathSum = function(root, targetSum) {
    if(!root) return  false;
    let ans = false;

    function calcPathSum(curr, currSum){
        let newSum = currSum + curr.val;
        if(!curr.left && !curr.right){
          if(newSum == targetSum){
            ans = ans || true;
          }
        }
        let leftResult = curr.left && calcPathSum(curr.left , newSum);
        let rightResult = curr.right && calcPathSum(curr.right , newSum);
        return ans;
    }
    return calcPathSum(root ,0);
};
~~~

**Explanation:** All recursive calls access the same outer ans variable through a closure. Once a matching leaf sets ans to true, later calls do not reset it. The variables leftResult and rightResult are assigned but never used.

### Shared-state dry run

~~~text
       5
      / \
     4   8       target = 9

calcPathSum(5,0) → newSum=5; ans=false
  calcPathSum(4,5) → newSum=9; leaf matches; ans=true
  calcPathSum(8,5) → newSum=13; no match; ans stays true
Return ans → true
~~~

**Pitfalls:** Setting ans to true does **not** automatically stop remaining recursive calls. The expression ans || true can simply be ans = true. For early stopping, add an ans guard and return the outer ans after traversal, or use Approach 1/4.

**Time:** O(n). **Stack:** O(h). **Shared extra state:** O(1).

## Approach 4 — Remaining-target DFS (subtract node values)

Your latest solution:

~~~js
var hasPathSum = function(root, targetSum) {
    if(!root) return  false;

//bottom up approach
    function pathSum(curr , target){
        if(!curr) return false;

        let newTarget = target - curr.val;

        if(!curr.left && !curr.right){
            return newTarget == 0;
        }
        return  pathSum(curr.left , newTarget) ||   pathSum(curr.right , newTarget);
    }

    return pathSum(root, targetSum)
};
~~~

**Terminology correction:** Although the comment says bottom-up, this is **top-down remaining-target DFS**. The target decreases as calls descend; boolean answers return upward.

### Recursive call tree and return-value tree

~~~text
          5
         / \
        4   8
       /
      11
     /  \
    7    2

Target 22

pathSum(5,22): 22 - 5 = 17
└── pathSum(4,17): 17 - 4 = 13
    └── pathSum(11,13): 13 - 11 = 2
        ├── pathSum(7,2): 2 - 7 = -5 → false (leaf)
        └── pathSum(2,2): 2 - 2 = 0 → true (leaf)
             ↑ false || true → true at node 11
             ↑ true returns to node 4
             ↑ true returns to node 5
Final: true
~~~

~~~mermaid
flowchart TD
    A[DFS node and remaining target] --> B{Node null?}
    B -- Yes --> C[Return false]
    B -- No --> D[Subtract node value]
    D --> E{Leaf?}
    E -- Yes --> F[Return remaining equals zero]
    E -- No --> G[DFS left with remaining]
    G --> H{Left true?}
    H -- Yes --> I[Return true]
    H -- No --> J[DFS right with remaining]
~~~

**Why leaf-only?** A target can reach zero at an internal node, but the path must continue to a leaf. Negative node values are allowed, so do not stop merely because the remaining target becomes negative.

**Time:** O(n). **Stack:** O(h).

## Comparison — all four user approaches

| Approach | Downward state | Upward result | Short-circuit | Time | Stack |
|---|---|---|---|---|---|
| 1. Running sum + OR | Running sum | Boolean return | Yes | O(n) | O(h) |
| 2. Explicit results | Running sum | Two booleans | No, original | O(n) | O(h) |
| 3. Shared ans | Running sum | Shared variable | No, original | O(n) | O(h) |
| 4. Remaining target + OR | Decreasing target | Boolean return | Yes | O(n) | O(h) |

**Recommendation:** Approach 4 and Approach 1 are the cleanest interview solutions. Approach 2 clarifies return propagation, and Approach 3 teaches shared closure state.

## Corner cases and common mistakes

| Case | Expected |
|---|---|
| Empty tree, even target 0 | false |
| Single node 5, target 5 | true |
| Single node 5, target 0 | false |
| Internal node matches but leaf does not: [1,2], target 1 | false |
| Single node 0, target 0 | true |
| Negative path: [-2,null,-3], target -5 | true |
| Skewed tree | Works, O(n) stack worst case |
| Both subtrees have valid paths | true |

Remember: a leaf means **both children null**; OR evaluates its right operand only if the left is falsy; and returning true inside a helper must be propagated to the outer function unless using shared state.

**Recursion memory rule:** Approach 1 adds sum downward; Approach 4 subtracts target downward. Both return booleans upward.
