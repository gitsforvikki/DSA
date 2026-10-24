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
