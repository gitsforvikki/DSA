# 160. Intersection of Two Linked Lists

## 160. Intersection of Two Linked Lists — Two Solutions

**Problem:** https://leetcode.com/problems/intersection-of-two-linked-lists/ · **Easy** · **Solved**

Find the first **shared node reference** between two singly linked lists, or return `null`. Nodes with equal `val` are *not* necessarily the same node.

### Approach 1: Two Pointers + Length Difference (original solution)

```javascript
var getIntersectionNode = function(headA, headB) {
   let lengthA = 0;
   let lengthB = 0;

    let pA = headA;
    let pB = headB;
// calculate length of list A
   while(pA){
    pA = pA.next;
    lengthA++;
   }
// calculate length list B
   while(pB){
    pB = pB.next;
    lengthB++;
   }

   let diff  = Math.abs(lengthA - lengthB);

 // now move the long pointer to by difference
   pA = headA;
   pB = headB;
   if(lengthA > lengthB){
        for(let i = 0; i < diff ; i++){
            pA = pA.next;
        }
   }else{
        for(let i = 0; i < diff ; i++){
                pB = pB.next;
        }
   }

   // both pointer would be at same distance from intersecting node.
   while(pA){
    if(pA == pB){
        return pA;
    }
    pA = pA.next;
    pB = pB.next;
   }

   return null;
};
```

**Explanation:** Count the length of each list. Move the pointer in the longer list forward by the absolute length difference. Both pointers then have an equal number of nodes left to traverse. Advance them together; the first time their **references** match, return that node. Otherwise, return `null`.

```text
A: [4] → [1] ───────────┐
                       ↓
                      [8] → [4] → [5] → null
                       ↑
B: [5] → [6] → [1] ────┘

A length = 5; B length = 6; diff = 1.
Advance pB by one node:

pA: [4] → [1] → [8] → [4] → [5]
pB: [6] → [1] → [8] → [4] → [5]
                 ↑ both eventually reach SAME node 8
```

| Step | pA | pB | Same node reference? |
|---:|---|---|---|
| 1 | A's 4 | B's 6 | No |
| 2 | A's 1 | B's 1 | No — same value, different nodes |
| 3 | shared 8 | shared 8 | **Yes → return shared 8** |

**Time:** O(m+n). **Extra space:** O(1).

### Approach 2: Two Pointers + Pointer Switching (original solution)

```javascript
var getIntersectionNode = function(headA, headB) {

    let pA = headA;
    let pB = headB;
    while( pA != pB){
        pA = pA == null ? headB : pA.next;
        pB = pB == null ? headA : pB.next;
    }
    return pA;
}
```

**Explanation:** Start one pointer at each head. Every step, advance both pointers. When `pA` reaches `null`, redirect it to `headB`; when `pB` reaches `null`, redirect it to `headA`. Each pointer then walks a combined distance of **A+B** or **B+A**, balancing unequal list lengths without measuring them. If there is an intersection, both meet at the first shared node. If there isn't, both eventually become `null` at the same time and the loop stops.

**Pointer dry run (same example):**

| Step | pA | pB |
|---:|---|---|
| 0 | A4 | B5 |
| 1 | A1 | B6 |
| 2 | C8 | B1 |
| 3 | C4 | C8 |
| 4 | C5 | C4 |
| 5 | null | C5 |
| 6 | B5 | null |
| 7 | B6 | A4 |
| 8 | B1 | A1 |
| 9 | **C8** | **C8** |

Here `C8` is the **same shared node object**, not merely equal values.

**Time:** O(m+n). **Extra space:** O(1).

### Comparison

| Property | Length Alignment | Pointer Switching |
|---|---|---|
| Measure list lengths | Yes | No |
| Time | O(m+n) | O(m+n) |
| Extra space | O(1) | O(1) |
| Key idea | Align remaining distances | Traverse A+B and B+A |
| No intersection | Return null | Both pointers meet at null |

**Edge cases:** Empty lists; heads are already the same node; equal lengths; shared last node; same values but distinct nodes; no intersection. Use `===` instead of `==` as a JavaScript style improvement when comparing references.

**Counting:** Two approaches to LeetCode 160 count as **one** solved problem.

[← Two Pointers & Sliding Window sheet](README.md) · [Dashboard](../DASHBOARD.md)
