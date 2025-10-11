# Turing-Style Algorithm Problems – TypeScript Solutions

Each problem includes a brief prompt, approach, complexity, and a clean TypeScript solution.

---

## 1) Two-Sum (Indices)

**Prompt:** Given nums and target, return indices i, j (i < j) with nums[i] + nums[j] = target, else [-1, -1].
**Approach:** Use a hash map from value -> index; for each x, check target - x.
**Complexity:** Time O(n), Space O(n).

```ts
function twoSum(nums: number[], target: number): [number, number] {
  const seen = new Map<number, number>();
  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (seen.has(need)) return [seen.get(need)!, i];
    seen.set(nums[i], i);
  }
  return [-1, -1];
}
```

**Step-by-step:**

1. Create an empty hash map `seen` from value → index.
2. Iterate `i` through the array.
3. Compute `need = target - nums[i]`.
4. If `seen` already contains `need`, we have a pair; return `[seen.get(need), i]`.
5. Otherwise put the current number and its index into `seen`.
6. If the loop ends with no pair, return `[-1, -1]`.
7. Correctness relies on the fact that if `a + b = target`, when we see the second term we will have already stored the first.

## 2) Longest Substring Without Repeating Characters

**Prompt:** Return the length of the longest substring with all unique characters.
**Approach:** Sliding window with last-seen index per character.
**Complexity:** Time O(n), Space O(k) where k is charset size.

```ts
function lengthOfLongestSubstring(s: string): number {
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

**Step-by-step:**

1. Maintain a map `last` from character → most recent index.
2. Keep a sliding window `[left, right]` with unique characters.
3. Expand `right` one step at a time.
4. If `s[right]` was seen inside the window, move `left` to `last[s[right]] + 1`.
5. Update `last[s[right]] = right`.
6. Update `best = max(best, windowLength)`.
7. Return `best` after the loop.

## 3) Product of Array Except Self (No Division)

**Prompt:** Return ans[i] = product of all elements except nums[i].
**Approach:** Prefix and suffix passes; multiply into result.
**Complexity:** Time O(n), Space O(1) extra (excluding output).

```ts
function productExceptSelf(nums: number[]): number[] {
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

**Step-by-step:**

1. Initialize `res` with 1s.
2. Prefix pass: for each `i`, set `res[i] = product(nums[0..i-1])` using a running product `pref`.
3. Suffix pass: walk from right to left with running product `suff` = `product(nums[i+1..end])`.
4. Multiply into `res[i] *= suff` so `res[i]` becomes prefix × suffix.
5. Return `res`. Uses O(1) extra space beyond output.

## 4) Valid Parentheses

**Prompt:** Given brackets ()[]{} determine if the string is valid (properly nested).
**Approach:** Use a stack and a matching map.
**Complexity:** Time O(n), Space O(n).

```ts
function isValid(s: string): boolean {
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

**Step-by-step:**

1. Use a stack to store opening brackets.
2. For each character:

   * If opening, push to stack.
   * If closing, check the top of the stack matches the required opener; otherwise return `false`.
3. At the end, the stack must be empty for the string to be valid.

## 5) Merge Intervals

**Prompt:** Merge overlapping intervals [start, end].
**Approach:** Sort by start then sweep and merge.
**Complexity:** Time O(n log n), Space O(1) extra aside from output.

```ts
function merge(intervals: number[][]): number[][] {
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

**Step-by-step:**

1. Sort intervals by start time.
2. Initialize the current merge window `[s, e]` with the first interval.
3. For each next interval `[ns, ne]`:

   * If it overlaps (`ns <= e`), extend `e = max(e, ne)`.
   * Otherwise, push `[s, e]` to output and reset `[s, e] = [ns, ne]`.
4. Push the final `[s, e]` and return the result.

## 6) K Most Frequent Elements

**Prompt:** Return the k most frequent numbers in nums.
**Approach:** Count with a hash map, then bucket by frequency.
**Complexity:** Time O(n), Space O(n).

```ts
function topKFrequent(nums: number[], k: number): number[] {
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

**Step-by-step:**

1. Count occurrences in a `freq` map.
2. Create `buckets[f]` to list values that appear exactly `f` times.
3. Fill the buckets from the frequency map.
4. Walk frequencies from high to low, collecting values until you have `k`.
5. Return the collected values. (Alternative: use a size-`k` min-heap.)

## 7) Binary Tree Level Order (BFS)

**Prompt:** Return nodes level by level.
**Approach:** Use a queue and collect values per level.
**Complexity:** Time O(n), Space O(n).

```ts
class TreeNode {
  val: number;
  left: TreeNode | null;
  right: TreeNode | null;
  constructor(val = 0, left: TreeNode | null = null, right: TreeNode | null = null) {
    this.val = val; this.left = left; this.right = right;
  }
}
function levelOrder(root: TreeNode | null): number[][] {
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

**Step-by-step:**

1. If `root` is null, return `[]`.
2. Initialize a queue with `root`.
3. While the queue is not empty:

   * Record the current `size` (nodes in this level).
   * Pop exactly `size` nodes, push their values to `level`, and enqueue their non-null children.
   * Append `level` to `res`.
4. Return `res`.

## 8) Lowest Common Ancestor (BST)

**Prompt:** Given a BST and two nodes p, q, return their LCA.
**Approach:** Traverse using ordering: if both < root go left; if both > root go right; else root is LCA.
**Complexity:** Time O(h), Space O(1).

```ts
function lowestCommonAncestor(root: TreeNode | null, p: TreeNode, q: TreeNode): TreeNode | null {
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

**Step-by-step:**

1. Let `a = min(p.val, q.val)` and `b = max(p.val, q.val)`.
2. Start from `root` and walk down the BST:

   * If `b < cur.val`, both nodes lie in the left subtree → go left.
   * Else if `a > cur.val`, both lie in the right subtree → go right.
   * Otherwise the nodes split around `cur` (or one equals `cur`) → `cur` is the LCA.
3. Return the found node (or `null` if not present).

## 9) Number of Islands

**Prompt:** Count islands ('1') in a grid using 4-directional adjacency.
**Approach:** DFS/BFS flood fill marking visited.
**Complexity:** Time O(m·n), Space O(m·n).

```ts
function numIslands(grid: string[][]): number {
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

**Step-by-step:**

1. Iterate all cells of the grid.
2. When you see land `'1'`, increment `count` and start a DFS/BFS from that cell.
3. In the flood fill, mark visited land as `'0'` to avoid revisiting.
4. Explore 4-directional neighbors and continue until the island is fully erased.
5. Continue scanning for the next unvisited island. Return `count`.

## 10) Coin Change (Min Coins)

**Prompt:** Given coins and amount, return min coins to make amount or -1.
**Approach:** Bottom-up DP where dp[a] = 1 + min(dp[a - coin]).
**Complexity:** Time O(n·amount), Space O(amount).

```ts
function coinChange(coins: number[], amount: number): number {
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

**Step-by-step:**

1. Create `dp[0..amount]`, initialize with `INF`, and set `dp[0] = 0`.
2. For each amount `a = 1..amount`:

   * For each coin `c`, if `a - c >= 0`, try using it: `dp[a] = min(dp[a], dp[a-c] + 1)`.
3. If `dp[amount]` is still `INF`, return `-1` (impossible); otherwise return `dp[amount]`.

## 11) Longest Increasing Subsequence (LIS)

**Prompt:** Return the length of the LIS.
**Approach:** Tails array + binary search (patience sorting).
**Complexity:** Time O(n log n), Space O(n).

```ts
function lengthOfLIS(nums: number[]): number {
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

**Step-by-step:**

1. Maintain `tails[i]` = smallest tail value of an increasing subsequence of length `i+1`.
2. For each number `x`:

   * Binary-search the first index `lo` where `tails[lo] >= x`.
   * Set `tails[lo] = x` (this keeps tails minimal and future-friendly).
3. The length of `tails` at the end equals the LIS length.

## 12) Median of Two Sorted Arrays

**Prompt:** Find the median of two sorted arrays.
**Approach:** Binary search partition on the smaller array.
**Complexity:** Time O(log(min(n, m))), Space O(1).

```ts
function findMedianSortedArrays(a: number[], b: number[]): number {
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

**Step-by-step:**

1. Ensure `a` is the smaller array to keep the binary search on the shorter one.
2. Binary-search a partition `i` in `a`; derive `j` so the left half has `(m+n+1)/2` items.
3. Compute border values `Aleft, Aright, Bleft, Bright` using sentinels ±∞ when off the ends.
4. If `Aleft <= Bright` and `Bleft <= Aright`, the partition is correct:

   * If total length is even, median is average of `max(Aleft, Bleft)` and `min(Aright, Bright)`.
   * Else, median is `max(Aleft, Bleft)`.
5. If `Aleft > Bright`, move `hi` left; else move `lo` right.
6. Loop until found; return result.
