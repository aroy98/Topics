# Algorithms – Python Edition

Each section includes **Problem**, **Explanation**, **Example Input**, **Expected Output**, **Code**, and **Test Cases**.

## 1) Two-Sum (Indices)

**Problem:** Given an array of integers nums and an integer target, return indices i, j (i < j) such that nums[i] + nums[j] == target. If no pair exists, return [-1, -1].

**Explanation:** Use a hash map to remember the index of each value. For each x, look for target−x already seen; if found, we've got our answer in O(1) expected time per element.

**Example Input:** `nums = [2,7,11,15], target = 9`

**Expected Output:** `[0,1]`

```python
def two_sum(nums, target):
    seen = {}
    for i, x in enumerate(nums):
        need = target - x
        if need in seen:
            return [seen[need], i]
        seen[x] = i
    return [-1, -1]
```

### Test Cases

| Input                | Expected  |
| -------------------- | --------- |
| `[3,2,4], target=6`  | `[1,2]`   |
| `[3,3], target=6`    | `[0,1]`   |
| `[1,2,5], target=10` | `[-1,-1]` |

## 2) Longest Substring Without Repeating Characters

**Problem:** Given a string s, find the length of the longest substring without repeating characters.

**Explanation:** Maintain a sliding window [left, right]. Track last seen index for each char. When a repeat is inside the window, move left just after the previous occurrence.

**Example Input:** `s = "pwwkew"`

**Expected Output:** `3`

```python
def length_of_longest_substring(s: str) -> int:
    last = {}
    best = left = 0
    for right, ch in enumerate(s):
        if ch in last and last[ch] >= left:
            left = last[ch] + 1
        last[ch] = right
        best = max(best, right - left + 1)
    return best
```

### Test Cases

| Input        | Expected |
| ------------ | -------- |
| `"abcabcbb"` | `3`      |
| `"bbbbb"`    | `1`      |
| `""`         | `0`      |

## 3) Product of Array Except Self

**Problem:** Given an integer array nums, return an array answer such that answer[i] is the product of all elements of nums except nums[i]. No division allowed.

**Explanation:** Compute prefix products in one pass and suffix products in reverse, multiplying into the output to achieve O(1) extra space (excluding output).

**Example Input:** `nums = [1,2,3,4]`

**Expected Output:** `[24,12,8,6]`

```python
def product_except_self(nums):
    n = len(nums)
    res = [1] * n
    pref = 1
    for i in range(n):
        res[i] = pref
        pref *= nums[i]
    suff = 1
    for i in range(n-1, -1, -1):
        res[i] *= suff
        suff *= nums[i]
    return res
```

### Test Cases

| Input           | Expected        |
| --------------- | --------------- |
| `[2,3,4,5]`     | `[60,40,30,24]` |
| `[1,0,3,0]`     | `[0,0,0,0]`     |
| `[-1,1,0,-3,3]` | `[0,0,9,0,0]`   |

## 4) Valid Parentheses

**Problem:** Given s with only '()[]{}', determine if it's valid (properly nested).

**Explanation:** Use a stack; push openers, verify match on closers.

**Example Input:** `s = "([]){}"`

**Expected Output:** `true`

```python
def is_valid(s: str) -> bool:
    open_set = set('([{')
    match = {')': '(', ']': '[', '}': '{'}
    st = []
    for ch in s:
        if ch in open_set:
            st.append(ch)
        else:
            if not st or st.pop() != match.get(ch, None):
                return False
    return len(st) == 0
```

### Test Cases

| Input      | Expected |
| ---------- | -------- |
| `"(]"`     | `false`  |
| `"({[]})"` | `true`   |
| `"("`      | `false`  |

---

## 5) Merge Intervals

**Problem:** Given an array of intervals where intervals[i] = [start_i, end_i], merge all overlapping intervals.

**Explanation:** Sort by start time, then sweep and merge when the current interval overlaps the ongoing merged window.

**Example Input:** `intervals = [[1,3],[2,6],[8,10],[15,18]]`

**Expected Output:** `[[1,6],[8,10],[15,18]]`

```python
def merge(intervals):
    if not intervals: return []
    intervals.sort(key=lambda x: x[0])
    res = []
    s, e = intervals[0]
    for ns, ne in intervals[1:]:
        if ns <= e:
            e = max(e, ne)
        else:
            res.append([s, e])
            s, e = ns, ne
    res.append([s, e])
    return res
```

### Test Cases

| Input                          | Expected                 |
| ------------------------------ | ------------------------ |
| `[[1,3],[2,6],[8,10],[15,18]]` | `[[1,6],[8,10],[15,18]]` |
| `[[1,4],[4,5]]`                | `[[1,5]]`                |
| `[[1,4],[0,0]]`                | `[[0,0],[1,4]]`          |

## 6) K Most Frequent Elements

**Problem:** Given an integer array nums and an integer k, return the k most frequent elements.

**Explanation:** Count with a hash map and bucket elements by frequency; read back from highest frequency downward until k elements are collected.

**Example Input:** `nums = [1,1,1,2,2,3], k = 2`

**Expected Output:** `[1,2]`

```python
from collections import Counter, defaultdict

def top_k_frequent(nums, k):
    cnt = Counter(nums)
    buckets = defaultdict(list)
    for v, f in cnt.items():
        buckets[f].append(v)
    res = []
    maxf = max(cnt.values()) if cnt else 0
    for f in range(maxf, 0, -1):
        for v in buckets.get(f, []):
            res.append(v)
            if len(res) == k:
                return res
    return res
```

### Test Cases

| Input                    | Expected                             |
| ------------------------ | ------------------------------------ |
| `[1,1,1,2,2,3], k=2`     | `[1,2]`                              |
| `[1], k=1`               | `[1]`                                |
| `[4,1,-1,2,-1,2,3], k=2` | `[-1,2]` (order among ties may vary) |

## 7) Binary Tree Level Order (BFS)

**Problem:** Given the root of a binary tree, return the level order traversal of its nodes' values.

**Explanation:** Use a queue; process nodes level by level, enqueuing children and collecting values per level.

**Example Input:** `root = [3,9,20,null,null,15,7]`

**Expected Output:** `[[3],[9,20],[15,7]]`

```python
from collections import deque

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def level_order(root):
    if not root: return []
    res, q = [], deque([root])
    while q:
        size = len(q)
        level = []
        for _ in range(size):
            node = q.popleft()
            level.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level)
    return res
```

### Test Cases

| Input                | Expected            |
| -------------------- | ------------------- |
| `root = []`          | `[]`                |
| `root = [1]`         | `[[1]]`             |
| `root = [1,2,3,4,5]` | `[[1],[2,3],[4,5]]` |

## 8) Lowest Common Ancestor (BST)

**Problem:** Given a Binary Search Tree (BST) and two nodes p and q, return their lowest common ancestor (LCA).

**Explanation:** Use BST ordering: if both targets are less than the current node, go left; if both greater, go right; otherwise current is the LCA.

**Example Input:** `root=[6,2,8,0,4,7,9,null,null,3,5], p=2, q=8`

**Expected Output:** `6`

```python
def lowest_common_ancestor(root, p, q):
    a, b = min(p.val, q.val), max(p.val, q.val)
    cur = root
    while cur:
        if b < cur.val:
            cur = cur.left
        elif a > cur.val:
            cur = cur.right
        else:
            return cur
    return None
```

### Test Cases

| Input      | Expected |
| ---------- | -------- |
| `p=2, q=4` | `2`      |
| `p=7, q=9` | `8`      |
| `p=3, q=5` | `4`      |

## 9) Number of Islands

**Problem:** Given a 2D grid of '1's (land) and '0's (water), count the number of islands. Islands are connected horizontally or vertically.

**Explanation:** Scan the grid; upon seeing land, increment count and flood fill (DFS/BFS) to mark the entire island as visited.

**Example Input:** `grid = [["1","1","0","0"],["1","0","0","1"],["0","0","1","1"]]`

**Expected Output:** `3`

```python
def num_islands(grid):
    if not grid: return 0
    R, C = len(grid), len(grid[0])
    def inb(r, c): return 0 <= r < R and 0 <= c < C
    count = 0
    for r in range(R):
        for c in range(C):
            if grid[r][c] == '1':
                count += 1
                stack = [(r, c)]
                grid[r][c] = '0'
                while stack:
                    i, j = stack.pop()
                    for di, dj in ((1,0),(-1,0),(0,1),(0,-1)):
                        ni, nj = i+di, j+dj
                        if inb(ni, nj) and grid[ni][nj] == '1':
                            grid[ni][nj] = '0'
                            stack.append((ni, nj))
    return count
```

### Test Cases

| Input                   | Expected |
| ----------------------- | -------- |
| `[["1"]]`               | `1`      |
| `[["0"]]`               | `0`      |
| `[["1","1"],["1","1"]]` | `1`      |

## 10) Coin Change (Min Coins)

**Problem:** Given coins of different denominations and a total amount, return the fewest number of coins that make up that amount. If impossible, return -1.

**Explanation:** Bottom-up DP: dp[a] = 1 + min(dp[a - coin]) over all coins that fit.

**Example Input:** `coins = [1,2,5], amount = 11`

**Expected Output:** `3`

```python
def coin_change(coins, amount):
    INF = 10**15
    dp = [INF] * (amount + 1)
    dp[0] = 0
    for a in range(1, amount + 1):
        for c in coins:
            if a - c >= 0:
                dp[a] = min(dp[a], dp[a - c] + 1)
    return -1 if dp[amount] >= INF else dp[amount]
```

### Test Cases

| Input                     | Expected |
| ------------------------- | -------- |
| `coins=[2], amount=3`     | `-1`     |
| `coins=[1], amount=0`     | `0`      |
| `coins=[1,3,4], amount=6` | `2`      |

## 11) Longest Increasing Subsequence (LIS)

**Problem:** Given an integer array nums, return the length of the longest strictly increasing subsequence.

**Explanation:** Maintain a 'tails' array where tails[i] is the smallest possible tail of an increasing subsequence of length i+1; place each x using binary search.

**Example Input:** `nums = [10,9,2,5,3,7,101,18]`

**Expected Output:** `4`

```python
def length_of_lis(nums):
    import bisect
    tails = []
    for x in nums:
        i = bisect.bisect_left(tails, x)
        if i == len(tails):
            tails.append(x)
        else:
            tails[i] = x
    return len(tails)
```

### Test Cases

| Input            | Expected |
| ---------------- | -------- |
| `[0,1,0,3,2,3]`  | `4`      |
| `[7,7,7,7]`      | `1`      |
| `[4,10,4,3,8,9]` | `3`      |

## 12) Median of Two Sorted Arrays

**Problem:** Given two sorted arrays a and b of size m and n respectively, return the median of the two sorted arrays.

**Explanation:** Binary search a partition in the smaller array so that left halves contain half the elements and all left-side values ≤ right-side values.

**Example Input:** `a = [1,3], b = [2]`

**Expected Output:** `2.0`

```python
def find_median_sorted_arrays(a, b):
    if len(a) > len(b):
        a, b = b, a
    m, n = len(a), len(b)
    lo, hi = 0, m
    while lo <= hi:
        i = (lo + hi) // 2
        j = (m + n + 1) // 2 - i
        Aleft = a[i-1] if i > 0 else float('-inf')
        Aright = a[i] if i < m else float('inf')
        Bleft = b[j-1] if j > 0 else float('-inf')
        Bright = b[j] if j < n else float('inf')
        if Aleft <= Bright and Bleft <= Aright:
            if (m + n) % 2 == 0:
                return (max(Aleft, Bleft) + min(Aright, Bright)) / 2.0
            else:
                return float(max(Aleft, Bleft))
        elif Aleft > Bright:
            hi = i - 1
        else:
            lo = i + 1
    return 0.0
```

### Test Cases

| Input              | Expected |
| ------------------ | -------- |
| `a=[1,2], b=[3,4]` | `2.5`    |
| `a=[], b=[1]`      | `1.0`    |
| `a=[0,0], b=[0,0]` | `0.0`    |

---

## Unified Test Suite (PyTest)

Save as `test_algorithms.py` and run with `pytest`.

```python
impor
```
