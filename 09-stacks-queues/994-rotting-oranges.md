# 994. Rotting Oranges — Multi-source BFS

[LeetCode 994](https://leetcode.com/problems/rotting-oranges/) · Medium · Queue / Multi-source BFS · **Solved**

## Problem in simple words

A grid contains `0` (empty), `1` (fresh orange), and `2` (rotten orange). Every minute, rotten oranges infect adjacent fresh oranges **up, down, left, right** (not diagonally). Find the minimum minutes until no fresh oranges remain, or return `-1` if impossible.

Example: `[[2,1,1],[1,1,0],[0,1,1]]` → `4`.

## Approach 1 — My original BFS solution (preserved)

```javascript
/**
 * @param {number[][]} grid
 * @return {number}
 */

var orangesRotting = function(grid) {
    let queue = [];
    let m = grid.length; //row
    let n = grid[0].length; //col

    //first push all the rotten oranges in the queue
    for(let i = 0; i < m; i++){
        for(let j=0; j<n; j++){
            if(grid[i][j] == 2){
                queue.push([i ,j,0]);
            }
        }
    }

    let maxMinut = 0;
    //run loop while queue not empty and make fresh oranges rotting next to the rotting oranges
    //after make new fresh orange rotting push it to the queue.
    while(queue.length){
        //take first element of queue and make each adjucent fresh to rotting
            let [i , j , level ] = queue.shift();

            //check for left orange
            if(j-1 >= 0 && grid[i][j-1] == 1){
                //make this left to rotting ans push in queue
                grid[i][j-1] = 2
                queue.push([i, j-1, level+1]);

            }
            //check for right and meke it rotting
            if(j+1 < n && grid[i][j+1] == 1){
                //make this right to rotting ans push in queue
                grid[i][j+1] = 2
                queue.push([i, j+1, level+1]);

            }
            // check for top and make it rotting
            if(i-1 >= 0 && grid[i-1][j] == 1){
                //make this top to rotting ans push in queue
                grid[i-1][j] = 2
                queue.push([i-1, j, level+1]);
            }

            // check for boton and make it rotting
            if(i+1 < m && grid[i+1][j] == 1){
                //make this bottom to rotting ans push in queue
                grid[i+1][j] = 2
                queue.push([i+1, j, level+1]);
            }
            maxMinut = Math.max(maxMinut,level)

    }

    //check is there any fresh orange remain or not
    for(let i = 0; i < m; i++){
        for(let j=0; j<n; j++){
            if(grid[i][j] == 1){
                return -1;
            }
        }
    }
    return maxMinut

};
```

### Why this works

1. Put **all initially rotten oranges** into the queue with time `0`. This is **multi-source BFS**: infection spreads from all starting points simultaneously.
2. Remove the next `[row, col, minute]` entry.
3. For each of four neighbors, check boundaries and whether its value is `1`.
4. Change a fresh neighbor to `2` **immediately**, and enqueue it with `minute + 1`. Marking immediately prevents duplicate visits.
5. Track the maximum processed minute. After BFS, return `-1` if any `1` remains; otherwise return that maximum.

### Direction graphic

```text
                  (i-1, j)
                     ↑
  (i, j-1)  ←  [i, j]  →  (i, j+1)
                     ↓
                  (i+1, j)
```

Do not use `i++`, `j--` inside checks: those mutate the current position. Use `i + 1`, `j - 1` instead.

### Minute-by-minute grid graphic

Legend: `R` rotten, `F` fresh, `.` empty.

```text
Minute 0       Minute 1       Minute 2
R F F          R R F          R R R
F F .          R F .          R R .
. F F          . F F          . F F

Minute 3       Minute 4
R R R          R R R
R R .          R R .
. R F          . R R
```

### Queue timeline (selected entries)

```text
Start:    [(0,0,0)]
Minute 1: (0,1,1), (1,0,1)
Minute 2: (0,2,2), (1,1,2)
Minute 3: (2,1,3)
Minute 4: (2,2,4)
Answer: 4
```

### Complexity note for original version

Each cell is enqueued at most once, so BFS visits `O(m × n)` cells. But JavaScript's `Array.shift()` can reindex the remaining queue, costing `O(Q)` per removal. **Worst-case runtime: O((m × n)²)** with this array-backed queue; **space: O(m × n)**. The algorithmic BFS traversal itself is linear.

---

## Approach 2 — Optimized BFS using a front pointer

Same idea, same four direction checks, but avoid `shift()`. A `front` index advances through the array without removing its first element.

```javascript
/**
 * @param {number[][]} grid
 * @return {number}
 */
var orangesRotting = function(grid) {
    const m = grid.length;
    const n = grid[0].length;
    const queue = [];

    for (let i = 0; i < m; i++) {
        for (let j = 0; j < n; j++) {
            if (grid[i][j] === 2) {
                queue.push([i, j, 0]);
            }
        }
    }

    let front = 0;
    let maxMinut = 0;

    while (front < queue.length) {
        const [i, j, level] = queue[front++];

        if (j - 1 >= 0 && grid[i][j - 1] === 1) {
            grid[i][j - 1] = 2;
            queue.push([i, j - 1, level + 1]);
        }
        if (j + 1 < n && grid[i][j + 1] === 1) {
            grid[i][j + 1] = 2;
            queue.push([i, j + 1, level + 1]);
        }
        if (i - 1 >= 0 && grid[i - 1][j] === 1) {
            grid[i - 1][j] = 2;
            queue.push([i - 1, j, level + 1]);
        }
        if (i + 1 < m && grid[i + 1][j] === 1) {
            grid[i + 1][j] = 2;
            queue.push([i + 1, j, level + 1]);
        }

        maxMinut = Math.max(maxMinut, level);
    }

    for (let i = 0; i < m; i++) {
        for (let j = 0; j < n; j++) {
            if (grid[i][j] === 1) return -1;
        }
    }

    return maxMinut;
};
```

### How the front pointer works

```text
queue = [A, B, C, D]
         ^
       front = 0  → process A

queue = [A, B, C, D, E]
            ^
          front = 1  → process B

Nothing is shifted. New oranges are appended to the end.
```

**Time:** `O(m × n)`. **Space:** `O(m × n)`. Each cell is queued at most once, and advancing `front` is `O(1)`.

## Test cases

| Input grid | Expected | Reason |
|---|---:|---|
| `[[2,1,1],[1,1,0],[0,1,1]]` | 4 | All fresh oranges reachable |
| `[[2,1,1],[0,1,1],[1,0,1]]` | -1 | One fresh orange unreachable |
| `[[0,2]]` | 0 | No fresh oranges |
| `[[1]]` | -1 | Fresh orange with no rotten source |
| `[[0]]` | 0 | Empty cell only |

## Compare

| Feature | Original | Optimized |
|---|---|---|
| BFS technique | Multi-source | Multi-source |
| Queue removal | `queue.shift()` | `queue[front++]` |
| Worst-case time in JS | O((m × n)²) | **O(m × n)** |
| Extra space | O(m × n) | O(m × n) |

**Interview takeaway:** This is a **multi-source BFS shortest-time** problem. Queue all initial rotten oranges at minute 0, infect neighbors at minute +1, and check for unreachable fresh oranges at the end. The optimized version keeps your original logic while improving queue performance.

[← Stack & Queue index](./README.md)
