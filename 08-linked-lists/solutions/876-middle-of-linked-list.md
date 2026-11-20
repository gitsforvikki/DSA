# 876. Middle of the Linked List — LeetCode 876

**Difficulty:** Easy  
**Problem:** https://leetcode.com/problems/middle-of-the-linked-list/  
**Practice sheet:** [Linked Lists](../linked-lists.md)

## Problem

Given the head of a singly linked list, return the middle node. If there are two middle nodes, return the **second** middle node.

```text
Odd length:  1 → 2 → 3 → 4 → 5 → null
                  ↑
               middle

Even length: 1 → 2 → 3 → 4 → 5 → 6 → null
                      ↑
                   middle (second of 3 and 4)
```

## Your JavaScript solution

```javascript
var middleNode = function(head) {
    let slow = head;
    let fast = head;

    while (fast && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    return slow;
};
```

## How it works — slow and fast pointers

- `slow` moves **one step** at a time.
- `fast` moves **two steps** at a time.
- When `fast` reaches the end (or cannot take another two-step move), `slow` is at the middle.
- For an even-length list, this loop condition makes `slow` land on the **second middle** node, as the problem requires.

### Dry run: odd number of nodes

```text
Start:
slow, fast
   ↓
   1 → 2 → 3 → 4 → 5 → null

After 1 loop:
        slow    fast
          ↓       ↓
   1 → 2 → 3 → 4 → 5 → null

After 2 loops:
                slow            fast
                  ↓               ↓
   1 → 2 → 3 → 4 → 5 → null

fast.next is null, so stop and return node 3.
```

### Dry run: even number of nodes

```text
Start:       slow, fast
                ↓
   1 → 2 → 3 → 4 → 5 → 6 → null

After 1 loop:
        slow        fast
          ↓           ↓
   1 → 2 → 3 → 4 → 5 → 6 → null

After 2 loops:
                slow                fast
                  ↓                   ↓
   1 → 2 → 3 → 4 → 5 → 6 → null

After 3 loops:
                        slow                 fast
                          ↓                    ↓
   1 → 2 → 3 → 4 → 5 → 6 → null

fast is null, so stop and return node 4 (the second middle).
```

## Why the loop condition matters

`while (fast && fast.next != null)` checks that `fast` exists and that its next node exists before moving `fast` by two links. This prevents accessing `next` on `null` and ensures the second middle is selected for even-length lists.

## Complexity

- **Time:** O(n) — the fast pointer traverses the list at two nodes per iteration.
- **Extra space:** O(1) — only two pointers are used.

## Edge cases to test

- `head = [1]` → `[1]`
- `head = [1, 2]` → `[2]`
- `head = [1, 2, 3]` → `[2, 3]`
- `head = [1, 2, 3, 4]` → `[3, 4]`
- `head = [1, 2, 3, 4, 5, 6]` → `[4, 5, 6]`

[← Back to practice sheet](../linked-lists.md)