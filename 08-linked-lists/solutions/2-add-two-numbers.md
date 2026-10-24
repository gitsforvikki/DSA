# LeetCode 2 — Add Two Numbers

[Problem on LeetCode](https://leetcode.com/problems/add-two-numbers/) · Medium · Linked List · Dummy Node + Carry · **Solved**

## Problem

Two linked lists represent non-negative integers, with digits in **reverse order**. Add the numbers and return the answer as a new linked list, also in reverse order.

```text
l1: 2 → 4 → 3    represents 342
l2: 5 → 6 → 4    represents 465
                 342 + 465 = 807
out: 7 → 0 → 8
```

## My solution (original code preserved)

```javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
    let curr1 = l1;
    let curr2 = l2;
    let carry = 0;
    let sentinel = new ListNode();
    let s = sentinel;

    while(curr1 || curr2 || carry){
        let val1 = curr1 ? curr1.val : 0;
        let val2 = curr2 ? curr2.val : 0;

        let total = val1 + val2 + carry;
        let toAdd = total % 10;
        carry = Math.floor(total /10);
        s.next = new ListNode(toAdd);
        s = s.next;
        s.next = null;


        curr1 = curr1 ? curr1.next : null;
        curr2 = curr2 ? curr2.next : null;
    }

    return sentinel.next;
};
```

## Explanation (simple)

1. `curr1` and `curr2` traverse the two lists. `carry` starts at 0.
2. `sentinel` is a **dummy node** before the real result. `s` points to the last result node, making append easy.
3. Continue while **either list still has a node OR carry is nonzero**.
4. Read each digit. If a list has ended, use 0.
5. `total = val1 + val2 + carry`.
6. `total % 10` is the digit for the new node. `Math.floor(total / 10)` is the carry for the next position.
7. Append `new ListNode(toAdd)`, move `s` forward, and safely advance both input pointers.
8. Return `sentinel.next` to skip the dummy node.

### Pointer graphic

```text
Before appending:
sentinel → [7] → [0] → null
                    ↑
                    s

Append next digit 8:
s.next = new ListNode(8)
sentinel → [7] → [0] → [8] → null
                           ↑
                           s = s.next

Return sentinel.next (node 7), not sentinel itself.
```

### Why the safe pointer checks matter

```javascript
curr1 = curr1 ? curr1.next : null;
curr2 = curr2 ? curr2.next : null;
```

If one list ends earlier, calling `null.next` would crash. These checks let the other list continue. `s.next = null` in the original code is **redundant but harmless**, because the provided `ListNode` constructor defaults `next` to `null`.

## Dry run: carry across digits

```text
l1: 9 → 9 → 9
l2: 1 → 0 → 0
```

| Step | val1 | val2 | Carry in | Total | Digit | Carry out |
|---|---:|---:|---:|---:|---:|---:|
| 1 | 9 | 1 | 0 | 10 | 0 | 1 |
| 2 | 9 | 0 | 1 | 10 | 0 | 1 |
| 3 | 9 | 0 | 1 | 10 | 0 | 1 |
| 4 | 0 | 0 | 1 | 1 | 1 | 0 |

```text
sentinel → 0 → 0 → 0 → 1 → null
           ↑ result starts here

999 + 1 = 1000
```

**Important:** `while (curr1 || curr2 || carry)` handles the final extra digit when both lists have ended but carry is still 1.

## Edge cases

| Input lists | Output | Why |
|---|---|---|
| `[2,4,3]` + `[5,6,4]` | `[7,0,8]` | Standard example |
| `[0]` + `[0]` | `[0]` | Zero |
| `[9,9]` + `[1]` | `[0,0,1]` | Different lengths and final carry |
| `[9,9,9]` + `[1,0,0]` | `[0,0,0,1]` | Carry creates an extra node |

## Complexity

Let `m` and `n` be the two list lengths.

- **Time:** `O(max(m,n))`, one pass through the longer list.
- **Output space:** `O(max(m,n))` nodes, plus at most one extra carry node.
- **Auxiliary space:** `O(1)` excluding the result list.

**Takeaway:** A dummy node makes building a linked list simpler. The carry condition and null-safe pointer advancement are the key edge cases.

[← Linked List question sheet](../linked-lists.md) · [← Section README](../README.md)
