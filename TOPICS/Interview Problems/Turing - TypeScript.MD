# Algorithms – TypeScript Edition

Each section includes **Problem**, **Explanation**, **Example Input**, **Expected Output**, and **Code**.

## 1) Two-Sum (Indices)

**Problem:** Given an array of integers nums and an integer target, return indices i, j (i < j) such that nums[i] + nums[j] == target. If no pair exists, return [-1, -1].

**Explanation:** Use a hash map to remember the index of each value. For each x, we look for target−x already seen; if found, we've got our answer in O(1) expected time per element.

**Example Input:** `nums = [2,7,11,15], target = 9`

**Expected Output:** `[0,1]`

```ts
export function twoSum(nums: number[], target: number): [number, number] {
  const seen = new Map<number, number>();
  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (seen.has(need)) return [seen.get(need)!, i];
    seen.set(nums[i], i);
  }
  return [-1, -1];
}
```

## 2) Longest Substring Without Repeating Characters

**Problem:** Given a string s, find the length of the longest substring without repeating characters.

**Explanation:** Maintain a sliding window [left, right]. Track last seen index for each char. When a repeat is inside the window, move left just after the previous occurrence.

**Example Input:** `s = "pwwkew"`

**Expected Output:** `3`

```ts
export function lengthOfLongestSubstring(s: string): number {
  const last = new Map<string, number>();
  let best = 0, left = 0;
  for (let right = 0; right < s.length; right++) {
    const ch = s[right];
    if (last.has(ch) && (last.get(ch) as number) >= left) {
      left = (last.get(ch) as number) + 1;
    }
    last.set(ch, right);
    best = Math.max(best, right - left + 1);
  }
  return best;
}
```

## 3) Product of Array Except Self

**Problem:** Given an integer array nums, return an array answer such that answer[i] is the product of all elements of nums except nums[i]. No division allowed.

**Explanation:** Compute prefix products in one pass and suffix products in reverse, multiplying into the output to achieve O(1) extra space (excluding output).

**Example Input:** `nums = [1,2,3,4]`

**Expected Output:** `[24,12,8,6]`

```ts
export function productExceptSelf(nums: number[]): number[] {
  const n = nums.length;
  const res = new Array<number>(n).fill(1);
  let pref = 1;
  for (let i = 0; i < n; i++) {
    res[i] = pref;
    pref *= nums[i];
  }
  let suff = 1;
  for (let i = n - 1; i >= 0; i--) {
    res[i] *= suff;
    suff *= nums[i];
  }
  return res;
}
```

## 4) Valid Parentheses

**Problem:** Given a string s containing just the characters '()[]{}', determine if the input string is valid (properly nested).

**Explanation:** Use a stack; push opening brackets, and on closing brackets verify it matches the top of the stack.

**Example Input:** `s = "([]){}"`

**Expected Output:** `true`

```ts
export function isValid(s: string): boolean {
  const open = new Set(['(', '[', '{']);
  const match = new Map<string, string>([[')', '('], [']', '['], ['}', '{']]);
  const st: string[] = [];
  for (const ch of s) {
    if (open.has(ch)) st.push(ch);
    else {
      const need = match.get(ch);
      if (!need || st.pop() !== need) return false;
    }
  }
  return st.length === 0;
}
```

## 5) Merge Intervals

**Problem:** Given an array of intervals where intervals[i] = [start_i, end_i], merge all overlapping intervals.

**Explanation:** Sort by start time, then sweep and merge when the current interval overlaps the ongoing merged window.

**Example Input:** `intervals = [[1,3],[2,6],[8,10],[15,18]]`

**Expected Output:** `[[1,6],[8,10],[15,18]]`

```ts
export function merge(intervals: number[][]): number[][] {
  if (intervals.length === 0) return [];
  intervals.sort((a, b) => a[0] - b[0]);
  const res: number[][] = [];
  let [s, e] = intervals[0];
  for (let i = 1; i < intervals.length; i++) {
    const [ns, ne] = intervals[i];
    if (ns <= e) e = Math.max(e, ne);
    else {
      res.push([s, e]);
      [s, e] = [ns, ne];
    }
  }
  res.push([s, e]);
  return res;
}
```

## 6) K Most Frequent Elements

**Problem:** Given an integer array nums and an integer k, return the k most frequent elements.

**Explanation:** Count with a hash map and bucket elements by frequency; read back from highest frequency downward until k elements are collected.

**Example Input:** `nums = [1,1,1,2,2,3], k = 2`

**Expected Output:** `[1,2]`

```ts
export function topKFrequent(nums: number[], k: number): number[] {
  const freq = new Map<number, number>();
  for (const x of nums) freq.set(x, (freq.get(x) || 0) + 1);
  const buckets: number[][] = Array(nums.length + 1).fill(0).map(() => []);
  for (const [val, f] of freq) buckets[f].push(val);
  const res: number[] = [];
  for (let f = buckets.length - 1; f >= 0 && res.length < k; f--) {
    for (const v of buckets[f]) {
      res.push(v);
      if (res.length === k) break;
    }
  }
  return res;
}
```

## 7) Binary Tree Level Order (BFS)

**Problem:** Given the root of a binary tree, return the level order traversal of its nodes' values.

**Explanation:** Use a queue; process nodes level by level, enqueuing children and collecting values per level.

**Example Input:** `root = [3,9,20,null,null,15,7]`

**Expected Output:** `[[3],[9,20],[15,7]]`

```ts
export class TreeNode {
  val: number; left: TreeNode | null; right: TreeNode | null;
  constructor(val = 0, left: TreeNode | null = null, right: TreeNode | null = null) {
    this.val = val; this.left = left; this.right = right;
  }
}
export function levelOrder(root: TreeNode | null): number[][] {
  if (!root) return [];
  const res: number[][] = [];
  const q: (TreeNode)[] = [root];
  while (q.length) {
    const size = q.length;
    const level: number[] = [];
    for (let i = 0; i < size; i++) {
      const node = q.shift() as TreeNode;
      level.push(node.val);
      if (node.left) q.push(node.left);
      if (node.right) q.push(node.right);
    }
    res.push(level);
  }
  return res;
}
```

## 8) Lowest Common Ancestor (BST)

**Problem:** Given a Binary Search Tree (BST) and two nodes p and q, return their lowest common ancestor (LCA).

**Explanation:** Use BST ordering: if both targets are less than the current node, go left; if both greater, go right; otherwise current is the LCA.

**Example Input:** `root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8`

**Expected Output:** `6`

```ts
export function lowestCommonAncestor(root: TreeNode | null, p: TreeNode, q: TreeNode): TreeNode | null {
  let cur = root;
  const a = Math.min(p.val, q.val);
  const b = Math.max(p.val, q.val);
  while (cur) {
    if (b < cur.val) cur = cur.left;
    else if (a > cur.val) cur = cur.right;
    else return cur;
  }
  return null;
}
```

## 9) Number of Islands

**Problem:** Given a 2D grid of '1's (land) and '0's (water), count the number of islands. Islands are connected horizontally or vertically.

**Explanation:** Scan the grid; upon seeing land, increment count and flood fill (DFS/BFS) to mark the entire island as visited.

**Example Input:** `grid = [["1","1","0","0"],["1","0","0","1"],["0","0","1","1"]]`

**Expected Output:** `3`

```ts
export function numIslands(grid: string[][]): number {
  const R = grid.length, C = R ? grid[0].length : 0;
  let count = 0;
  const dirs = [[1,0],[-1,0],[0,1],[0,-1]];
  const inb = (r: number, c: number) => r >= 0 && c >= 0 && r < R && c < C;
  for (let r = 0; r < R; r++) {
    for (let c = 0; c < C; c++) {
      if (grid[r][c] === '1') {
        count++;
        const stack: [number, number][] = [[r,c]];
        grid[r][c] = '0';
        while (stack.length) {
          const [i, j] = stack.pop() as [number, number];
          for (const [di, dj] of dirs) {
            const ni = i + di, nj = j + dj;
            if (inb(ni, nj) && grid[ni][nj] === '1') {
              grid[ni][nj] = '0';
              stack.push([ni, nj]);
            }
          }
        }
      }
    }
  }
  return count;
}
```

## 10) Coin Change (Min Coins)

**Problem:** Given coins of different denominations and a total amount, return the fewest number of coins that make up that amount. If impossible, return -1.

**Explanation:** Bottom-up DP: dp[a] = 1 + min(dp[a - coin]) over all coins that fit.

**Example Input:** `coins = [1,2,5], amount = 11`

**Expected Output:** `3`

```ts
export function coinChange(coins: number[], amount: number): number {
  const INF = 1e15;
  const dp = new Array<number>(amount + 1).fill(INF);
  dp[0] = 0;
  for (let a = 1; a <= amount; a++) {
    for (const c of coins) {
      if (a - c >= 0) dp[a] = Math.min(dp[a], dp[a - c] + 1);
    }
  }
  return dp[amount] >= INF ? -1 : dp[amount];
}
```

## 11) Longest Increasing Subsequence (LIS)

**Problem:** Given an integer array nums, return the length of the longest strictly increasing subsequence.

**Explanation:** Maintain a 'tails' array where tails[i] is the smallest possible tail of an increasing subsequence of length i+1; place each x using binary search.

**Example Input:** `nums = [10,9,2,5,3,7,101,18]`

**Expected Output:** `4`

```ts
export function lengthOfLIS(nums: number[]): number {
  const tails: number[] = [];
  for (const x of nums) {
    let lo = 0, hi = tails.length;
    while (lo < hi) {
      const mid = (lo + hi) >> 1;
      if (tails[mid] < x) lo = mid + 1; else hi = mid;
    }
    tails[lo] = x;
  }
  return tails.length;
}
```

## 12) Median of Two Sorted Arrays

**Problem:** Given two sorted arrays a and b of size m and n respectively, return the median of the two sorted arrays.

**Explanation:** Binary search a partition in the smaller array so that left halves contain half the elements and all left-side values ≤ right-side values.

**Example Input:** `a = [1,3], b = [2]`

**Expected Output:** `2.0`

```ts
export function findMedianSortedArrays(a: number[], b: number[]): number {
  if (a.length > b.length) return findMedianSortedArrays(b, a);
  const m = a.length, n = b.length;
  let lo = 0, hi = m;
  while (lo <= hi) {
    const i = (lo + hi) >> 1;
    const j = ((m + n + 1) >> 1) - i;
    const Aleft = i ? a[i - 1] : -Infinity;
    const Aright = i < m ? a[i] : Infinity;
    const Bleft = j ? b[j - 1] : -Infinity;
    const Bright = j < n ? b[j] : Infinity;
    if (Aleft <= Bright && Bleft <= Aright) {
      if (((m + n) & 1) === 0) {
        return (Math.max(Aleft, Bleft) + Math.min(Aright, Bright)) / 2;
      } else {
        return Math.max(Aleft, Bleft);
      }
    } else if (Aleft > Bright) hi = i - 1;
    else lo = i + 1;
  }
  return 0;
}
```

---

# ▶ Test Cases (TypeScript)

Below are compact runnable examples and expected results for each problem.

## 1) Two-Sum (Indices)

```ts
// Inputs -> Expected
// [2,7,11,15], 9 -> [0,1]
// [3,2,4], 6      -> [1,2]
// [1,2,5], 10     -> [-1,-1]
```

## 2) Longest Substring Without Repeating Characters

```ts
// "pwwkew"   -> 3
// "abcabcbb" -> 3
// "bbbbb"    -> 1
// ""         -> 0
```

## 3) Product of Array Except Self

```ts
// [1,2,3,4]        -> [24,12,8,6]
// [2,3,4,5]        -> [60,40,30,24]
// [-1,1,0,-3,3]    -> [0,0,9,0,0]
```

## 4) Valid Parentheses

```ts
// "([]){}"  -> true
// "(]"      -> false
// "({[]})"  -> true
// "("       -> false
```

## 5) Merge Intervals

```ts
// [[1,3],[2,6],[8,10],[15,18]] -> [[1,6],[8,10],[15,18]]
// [[1,4],[4,5]]                -> [[1,5]]
// [[1,4],[0,0]]                -> [[0,0],[1,4]]
```

## 6) K Most Frequent Elements

```ts
// [1,1,1,2,2,3], k=2 -> [1,2]
// [4,1,-1,2,-1,2,3], k=2 -> order among ties may vary
```

## 7) Binary Tree Level Order (BFS)

```ts
// [3,9,20,null,null,15,7] -> [[3],[9,20],[15,7]]
```

## 8) Lowest Common Ancestor (BST)

```ts
// root=[6,2,8,0,4,7,9,null,null,3,5], p=2, q=8 -> 6
```

## 9) Number of Islands

```ts
// [["1","1","0","0"],["1","0","0","1"],["0","0","1","1"]] -> 3
```

## 10) Coin Change (Min Coins)

```ts
// coins=[1,2,5], amount=11 -> 3
// coins=[2], amount=3 -> -1
```

## 11) Longest Increasing Subsequence (LIS)

```ts
// [10,9,2,5,3,7,101,18] -> 4
```

## 12) Median of Two Sorted Arrays

```ts
// a=[1,3], b=[2] -> 2.0
// a=[1,2], b=[3,4] -> 2.5
```

### Vitest Suite (optional)

Create `algorithms.test.ts` and paste this skeleton. Ensure your functions are exported from `turing_algorithms.ts`.

```ts
import { describe, it, expect } from 'vitest';
import { twoSum, lengthOfLongestSubstring, productExceptSelf, isValid, merge, topKFrequent, TreeNode, levelOrder, lowestCommonAncestor, numIslands, coinChange, lengthOfLIS, findMedianSortedArrays } from './turing_algorithms';

describe('Two-Sum', () => {
  it('basic', () => { expect(twoSum([2,7,11,15], 9)).toEqual([0,1]); });
});
// ... (full suite available in the downloadable files)
```
