# Section 07 — Recursion and Backtracking

**Weeks:** 12–13  
**Status:** Recursion basics practice started; remaining lessons not started  
**Study time:** 2 hours/day

## Learning objectives

Understand recursion, the base case, call stack, and when statements run before or after a recursive call. Keep the examples small and dry-run them.

## My small recursion practice

### 1. Print 1 to N

```javascript
function print(num) {
    if (num == 0) return;

    print(num - 1);
    console.log(num);
}

print(5);
// 1 2 3 4 5 (each on a new line)
```

**Why?** Calls go down as `print(5) → print(4) → ... → print(0)`. At `0`, the function returns. The `console.log` is **after** the recursive call, so it runs while returning: `1, 2, 3, 4, 5`.

### 2. Print N to 1

```javascript
function print(num) {
    if (num == 0) return;

    console.log(num);
    print(num - 1);
}

print(5);
// 5 4 3 2 1
```

**Why?** `console.log` is **before** the recursive call, so each number prints while calls go deeper.

### 3. Two ways of passing `num - 1` (different results!)

**Approach A — update the current local variable**

```javascript
function fun(num) {
    if (num == 0) return;

    num = num - 1;
    fun(num);
    console.log(num);
}

fun(5);
// 0 1 2 3 4
```

Here the current call's `num` changes from `5` to `4` **before** the next call. When this call resumes, it prints `4`. Likewise `fun(1)` changes its own `num` to `0`, then prints `0`.

**Approach B — calculate an argument without changing the current local variable**

```javascript
function fun(num) {
    if (num == 0) return;

    fun(num - 1);
    console.log(num);
}

fun(5);
// 1 2 3 4 5
```

Both approaches call the next function with the same smaller argument. But `fun(num - 1)` does **not** update the current call's `num`. Every recursive call has its own local parameter.

| Starting call | A: local num after update | B: local num unchanged |
| --- | --- | --- |
| `fun(5)` | 4 | 5 |
| `fun(4)` | 3 | 4 |
| `fun(3)` | 2 | 3 |
| `fun(2)` | 1 | 2 |
| `fun(1)` | 0 | 1 |

### 4. Print before AND after recursion

```javascript
function print(num) {
    if (num == 0) return;

    console.log(num);
    print(num - 1);
    console.log(num);
}

print(5);
// 5 4 3 2 1 1 2 3 4 5
```

**Call flow (down):**

```text
print(5) → log 5
  print(4) → log 4
    print(3) → log 3
      print(2) → log 2
        print(1) → log 1
          print(0) → return
```

**Return flow (up):**

```text
print(1) resumes → log 1
print(2) resumes → log 2
print(3) resumes → log 3
print(4) resumes → log 4
print(5) resumes → log 5
```

**Remember:** A recursive call pauses its caller until it finishes. Statements **before** recursion run while going down; statements **after** recursion run while returning. This example has one recursive child per call, so its recursion tree is a single chain.

### 5. Count people standing in a row (recursion)

**Question:** Five people are standing in a row. How can the first person find the total number of people using recursion?

**Idea:** Each person counts themselves as **1** and asks the next person how many people are behind them. The last person has no one behind them.

- **Base case:** No person (`null`) means `0`.
- **Recursive step:** `1 + countPeople(nextPerson)`.

```javascript
function countPeople(person) {
    if (person === null) return 0;

    return 1 + countPeople(person.next);
}

// Example: 5 people linked in a row
const p5 = { next: null };
const p4 = { next: p5 };
const p3 = { next: p4 };
const p2 = { next: p3 };
const p1 = { next: p2 };

console.log(countPeople(p1)); // 5
```

**Calls going down:**

```text
Person 1 → 1 + count(Person 2)
Person 2 → 1 + count(Person 3)
Person 3 → 1 + count(Person 4)
Person 4 → 1 + count(Person 5)
Person 5 → 1 + count(null)
                       └── 0 (base case)
```

**Answers coming back:**

```text
count(null)     = 0
count(Person 5) = 1 + 0 = 1
count(Person 4) = 1 + 1 = 2
count(Person 3) = 1 + 2 = 3
count(Person 2) = 1 + 3 = 4
count(Person 1) = 1 + 4 = 5
```

**Important:** Person 1 does **not** immediately return `1`. Their `1 + countPeople(person.next)` waits for the next person's result. Each earlier call resumes as the answers return. This is the same idea as counting nodes in a linked list.

**Complexity:** Time `O(N)` (visit each person once); space `O(N)` (recursive call stack).

### 6. Sum of an array — three recursive solutions

**Question:** Given `[1, 2, 3, 4, 5]`, return the sum (`15`) using recursion.

#### Solution 1 — Remove the first element with `slice()` (my first approach)

```javascript
function sum(arr) {
    if (arr.length == 0) return 0;

    return arr[0] + sum(arr.slice(1, arr.length));
}

console.log(sum([1, 2, 3, 4, 5])); // 15
```

- **Base case:** An empty array has sum `0`.
- **Recursive step:** Add the first element to the sum of the remaining elements.
- `slice(1, arr.length)` creates a **new array** without the first element; it does not modify the original array.

```text
sum([1,2,3,4,5]) = 1 + sum([2,3,4,5])
                  = 1 + 2 + sum([3,4,5])
                  = 1 + 2 + 3 + sum([4,5])
                  = 1 + 2 + 3 + 4 + sum([5])
                  = 1 + 2 + 3 + 4 + 5 + sum([])
                  = 1 + 2 + 3 + 4 + 5 + 0 = 15
```

**Complexity:** Time **O(N²)** and space **O(N²)** in the worst case: each `slice()` copies the remaining elements, and those arrays remain referenced during recursion. This solution is correct, but not the most efficient recursive version.

#### Solution 2 — Start at index 0 (left to right)

```javascript
function sum(arr, index = 0) {
    if (index == arr.length) return 0;

    return arr[index] + sum(arr, index + 1);
}

let test = [1, 2, 3, 4, 5];
let result = sum(test);
console.log(result); // 15
```

- **Base case:** `index == arr.length` means every element has been visited, so return `0`.
- **Recursive step:** Add `arr[index]` and move to `index + 1`.
- No array is copied. The original array stays unchanged.
- Also works for an empty array: `sum([])` returns `0`.

```text
sum(arr,0) → 1 + sum(arr,1)
               2 + sum(arr,2)
                     3 + sum(arr,3)
                           4 + sum(arr,4)
                                 5 + sum(arr,5)
                                       return 0

Return: 0 → 5 → 9 → 12 → 14 → 15
```

**Complexity:** Time **O(N)**; space **O(N)** for the call stack.

#### Solution 3 — Start at the last index (right to left)

**My original version:**

```javascript
function add(arr, lastIndex) {
    if (lastIndex == 0) return arr[lastIndex];

    return arr[lastIndex] + add(arr, lastIndex - 1);
}

let test = [1, 2, 3, 4, 5];
console.log(add(test, test.length - 1)); // 15
```

This is **correct for non-empty arrays**. It goes through indices `4 → 3 → 2 → 1 → 0`. However, for an empty array the starting index is `-1`, so `lastIndex == 0` is never reached.

**Improved version (handles empty arrays):**

```javascript
function add(arr, lastIndex = arr.length - 1) {
    if (lastIndex < 0) return 0;

    return arr[lastIndex] + add(arr, lastIndex - 1);
}

console.log(add([1, 2, 3, 4, 5])); // 15
console.log(add([]));               // 0
```

- **Base case:** `lastIndex < 0` means no elements remain, so return `0`.
- **Recursive step:** Add the current element and move left to `lastIndex - 1`.

```text
add(arr,4) → 5 + add(arr,3)
               4 + add(arr,2)
                     3 + add(arr,1)
                           2 + add(arr,0)
                                 1 + add(arr,-1)
                                       return 0

Return: 0 → 1 → 3 → 6 → 10 → 15
```

**Complexity:** Time **O(N)**; space **O(N)** for the call stack.

#### Comparison

| Solution | Direction | Time | Space | Empty array |
| --- | --- | --- | --- | --- |
| 1. `slice()` | First element + remaining array | O(N²) | O(N²) | Works |
| 2. Index from `0` | Left to right | O(N) | O(N) | Works |
| 3. Last index (improved) | Right to left | O(N) | O(N) | Works |

**Takeaway:** All three approaches compute the correct sum for non-empty arrays. The two index-based versions are more efficient because they avoid copying arrays. In each approach, the addition completes while recursive calls return.

### 7. LeetCode 231 — Power of Two

**Problem:** [Power of Two (LeetCode 231)](https://leetcode.com/problems/power-of-two/)

Given an integer `n`, return `true` if it is a power of two; otherwise return `false`.

**Examples:** `1 → true` (`2⁰`), `16 → true` (`2⁴`), `12 → false`, `0 → false`, `-2 → false`.

#### Solution 1 — Iterative (my solution; preferred for O(1) space)

```javascript
/**
 * @param {number} n
 * @return {boolean}
 */
var isPowerOfTwo = function(n) {
    if (n <= 0) return false;

    while (n % 2 == 0) {
        n = n / 2;
    }

    return n == 1;
};
```

**Explanation:**
1. A power of two must be positive, so return `false` for `n <= 0`.
2. While `n` is even, divide it by `2`.
3. If the remaining value is `1`, it was a power of two. Otherwise, it was not.

```text
n = 16: 16 → 8 → 4 → 2 → 1  => true
n = 12: 12 → 6 → 3          => false (3 is odd and not 1)
n = 1:  loop skipped        => true
```

**Time:** `O(log N)` for positive `N`. **Extra space:** `O(1)`.

#### Solution 2 — Recursive (my solution, corrected with return)

```javascript
/**
 * @param {number} n
 * @return {boolean}
 */
var isPowerOfTwo = function(n) {
    if (n == 1) return true;
    if (n % 2 != 0 || !Number.isInteger(n) || n <= 0) return false;

    return isPowerOfTwo(n / 2);
};
```

**Explanation:**
1. **True base case:** `n == 1`, because `1 = 2⁰`.
2. **False base case:** A non-positive, odd (other than 1), or non-integer value cannot be reduced to 1 by repeatedly dividing by 2.
3. **Recursive step:** Divide by `2` and **return** the recursive result. The original draft called `isPowerOfTwo(n / 2)` without `return`, which made the outer call return `undefined`.

```text
isPowerOfTwo(8)
  → isPowerOfTwo(4)
      → isPowerOfTwo(2)
          → isPowerOfTwo(1)
              → true
          ← true
      ← true
  ← true

isPowerOfTwo(6)
  → isPowerOfTwo(3)
      → false (odd)
  ← false
```

**Time:** `O(log N)` for positive `N`. **Extra space:** `O(log N)` due to the recursion call stack.

| Approach | Time | Extra space | Key point |
| --- | --- | --- | --- |
| Iterative | O(log N) | **O(1)** | Best space efficiency of these two |
| Recursive | O(log N) | O(log N) | Good recursion practice; remember `return` |

**Note:** LeetCode guarantees an integer input, so `Number.isInteger(n)` is optional there. Both solutions use repeated division, not bit manipulation.

## Lessons (topic outlines)

1. **Recursion and Call Stack** — Basic practice above
2. **Recursion Trees** — Not started
3. **Subsets and Permutations** — Not started
4. **Backtracking and Pruning** — Not started

## LeetCode practice

The following are starter problems. Solve them independently before marking complete.

- [ ] Subsets
- [ ] Permutations
- [ ] Combination Sum
- [ ] Generate Parentheses

## Section revision checklist

- [ ] Explain the key concepts in simple English
- [ ] Identify the appropriate pattern from problem clues
- [ ] Write JavaScript solutions without copying
- [ ] Analyze time and space complexity
- [ ] Re-solve missed problems independently

[← Main curriculum](../README.md) · [Progress tracker](../PROGRESS.md)
