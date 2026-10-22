# 21. Merge Two Sorted Lists — LeetCode 21

**Difficulty:** Easy  
**Problem:** https://leetcode.com/problems/merge-two-sorted-lists/  
**Practice sheet:** [Linked Lists](../linked-lists.md)

## Problem

Given two sorted linked lists, merge them into one sorted linked list by reusing the existing nodes.

## Think visually (before looking at the solution)

Example:

```text
list1:  1 ──→ 2 ──→ 4 ──→ null
list2:  1 ──→ 3 ──→ 4 ──→ null

output: 1 ──→ 1 ──→ 2 ──→ 3 ──→ 4 ──→ 4 ──→ null
```

Questions to ask yourself:
1. Which list's current node should come next?
2. How will you remember the beginning of the merged list?
3. What happens when one list becomes empty?
4. Do you need to create new nodes for every value?

<details>
<summary>Reveal pointer diagrams and dry run (after attempting)</summary>

A dummy node holds the start of the result, while `curr` stays at its tail.

```text
Initially:

dummy ──→ null
  ↑
 curr

list1 ──→ [1] ──→ [2] ──→ [4]
list2 ──→ [1] ──→ [3] ──→ [4]
```

| Step | Compare | Attach | Merged chain |
|---|---|---|---|
| 1 | 1 vs 1 | list1's 1 | 1 |
| 2 | 2 vs 1 | list2's 1 | 1 → 1 |
| 3 | 2 vs 3 | list1's 2 | 1 → 1 → 2 |
| 4 | 4 vs 3 | list2's 3 | 1 → 1 → 2 → 3 |
| 5 | 4 vs 4 | list1's 4 | 1 → 1 → 2 → 3 → 4 |
| 6 | list1 empty | attach rest of list2 | 1 → 1 → 2 → 3 → 4 → 4 |

```text
After two attachments:

dummy ──→ [1] ──→ [1] ──→ null
                       ↑
                      curr

list1: [2] ──→ [4] ──→ null
list2: [3] ──→ [4] ──→ null

After the loop:

dummy ──→ [1] ──→ [1] ──→ [2] ──→ [3] ──→ [4]
                                                    ↑
                                                   curr
list1: null
list2: [4] ──→ null

curr.next = list2

Result: [1] ──→ [1] ──→ [2] ──→ [3] ──→ [4] ──→ [4] ──→ null
```

**Key insight:** `curr.next = list1` or `list2` connects an existing node; then advance that list pointer and move `curr`. When either list ends, connect the entire remaining chain.

</details>

<details>
<summary>Reveal JavaScript solution</summary>

```javascript
var mergeTwoLists = function(list1, list2) {
    let dummy = new ListNode();
    let curr = dummy;

    while (list1 && list2) {
        if (list1.val <= list2.val) {
            curr.next = list1;
            list1 = list1.next;
        } else {
            curr.next = list2;
            list2 = list2.next;
        }

        curr = curr.next;
    }

    curr.next = list1 || list2;

    return dummy.next;
};
```

**Time:** O(n + m), where n and m are the list lengths.  
**Extra space:** O(1), because we reuse the nodes and only allocate one dummy node.

</details>

## Edge cases to test

- `list1 = [], list2 = []`
- `list1 = [], list2 = [0]`
- `list1 = [1], list2 = []`
- `list1 = [1, 1], list2 = [1, 1]`
- `list1 = [1, 3, 5], list2 = [2, 4, 6]`
- Lists with negative values or unequal lengths

[← Back to question sheet](../linked-lists.md)
