# Pattern Guides – TypeScript Edition

Concise guides for common patterns with **Problem, Explanation, Example I/O, Code, and Test Cases**.

## Index

* [A) Two Pointers (on a Sorted Array)](#a-two-pointers-on-a-sorted-array)
* [B) Sliding Window (Fixed Size)](#b-sliding-window-fixed-size)
* [C) Prefix Sum (Count Subarrays with Sum = k)](#c-prefix-sum-count-subarrays-with-sum--k)
* [D) Kadane's Algorithm (Max Subarray Sum)](#d-kadanes-algorithm-max-subarray-sum)
* [E) Top K Elements (Heap)](#e-top-k-elements-heap)
* [F) Monotonic Stack (Next Greater Element)](#f-monotonic-stack-next-greater-element)
* [G) Fast and Slow Pointers (Cycle Detection)](#g-fast-and-slow-pointers-cycle-detection)
* [H) How to Reverse a Linked List (Iterative)](#h-how-to-reverse-a-linked-list-iterative)
* [I) Level Order Traversal of Binary Tree](#i-level-order-traversal-of-binary-tree)

---

## A) Two Pointers (on a Sorted Array)

**Problem:** Given a **sorted** array `arr` and `target`, return a pair of values `(x, y)` such that `x + y = target`, or `null`.

**Explanation:** Start left at 0 and right at `n-1`. If sum too small → move left; if too large → move right.

**Example Input:** `arr = [1,2,3,4,6], target = 6`
**Expected Output:** `(2, 4)`

```ts
export function pairSumSorted(arr: number[], target: number): [number, number] | null {
  let i = 0, j = arr.length - 1;
  while (i < j) {
    const s = arr[i] + arr[j];
    if (s === target) return [arr[i], arr[j]];
    if (s < target) i++; else j--;
  }
  return null;
}
```

**Test Cases**

| Input            | Expected |
| ---------------- | -------- |
| `[1,2,3,4,6], 6` | `(2,4)`  |
| `[1,1,1,2], 3`   | `(1,2)`  |
| `[2,3,5,9], 10`  | `null`   |

---

## B) Sliding Window (Fixed Size)

**Problem:** Given `nums` and window size `k`, return the **maximum sum** of any contiguous subarray of length `k`.

**Explanation:** Build first window; slide by subtracting outgoing and adding incoming.

**Example Input:** `nums = [2,1,5,1,3,2], k = 3`
**Expected Output:** `9`

```ts
export function maxSumSubarrayK(nums: number[], k: number): number {
  if (k <= 0 || k > nums.length) return 0;
  let window = 0;
  for (let i = 0; i < k; i++) window += nums[i];
  let best = window;
  for (let r = k; r < nums.length; r++) {
    window += nums[r] - nums[r - k];
    if (window > best) best = window;
  }
  return best;
}
```

**Test Cases**

| Input                | Expected |
| -------------------- | -------- |
| `[2,1,5,1,3,2], k=3` | `9`      |
| `[1,2,3,4,5], k=2`   | `9`      |
| `[5], k=1`           | `5`      |

---

## C) Prefix Sum (Count Subarrays with Sum = k)

**Problem:** Count subarrays whose sum equals `k`.

**Explanation:** Maintain running prefix sum `p`; answer grows by how many times `p - k` has been seen.

**Example Input:** `nums = [1,2,3], k = 3`
**Expected Output:** `2`

```ts
export function countSubarraysSumK(nums: number[], k: number): number {
  const freq = new Map<number, number>();
  freq.set(0, 1);
  let prefix = 0, ans = 0;
  for (const x of nums) {
    prefix += x;
    ans += freq.get(prefix - k) || 0;
    freq.set(prefix, (freq.get(prefix) || 0) + 1);
  }
  return ans;
}
```

**Test Cases**

| Input           | Expected |
| --------------- | -------- |
| `[1,1,1], k=2`  | `2`      |
| `[1,2,3], k=3`  | `2`      |
| `[1,-1,0], k=0` | `3`      |

---

## D) Kadane's Algorithm (Max Subarray Sum)

**Problem:** Return the maximum sum of a contiguous subarray.

**Explanation:** Track `cur = max(x, cur + x)` and `best = max(best, cur)`.

**Example Input:** `[-2,1,-3,4,-1,2,1,-5,4]`
**Expected Output:** `6`

```ts
export function kadane(nums: number[]): number {
  let cur = nums[0], best = nums[0];
  for (let i = 1; i < nums.length; i++) {
    cur = Math.max(nums[i], cur + nums[i]);
    best = Math.max(best, cur);
  }
  return best;
}
```

**Test Cases**

| Input                     | Expected |
| ------------------------- | -------- |
| `[-2,1,-3,4,-1,2,1,-5,4]` | `6`      |
| `[1]`                     | `1`      |
| `[-1,-2,-3]`              | `-1`     |

---

## E) Top K Elements (Heap)

**Problem:** Return the `k` largest elements.

**Explanation:** Maintain a min-heap of size `k`.

**Example Input:** `[3,2,1,5,6,4], k=2`
**Expected Output:** `[6,5]` (order not important)

```ts
export function kLargest(nums: number[], k: number): number[] {
  if (k <= 0) return [];
  const heap: number[] = [];
  const push = (x: number) => { heap.push(x); siftUp(heap.length - 1); };
  const pop = () => { const top = heap[0]; const last = heap.pop()!; if (heap.length) { heap[0] = last; siftDown(0); } return top; };
  const siftUp = (i: number) => { while (i > 0) { const p = (i - 1) >> 1; if (heap[p] <= heap[i]) break; [heap[p], heap[i]] = [heap[i], heap[p]]; i = p; } };
  const siftDown = (i: number) => { const n = heap.length; while (true) { let l = 2*i+1, r = l+1, m = i; if (l < n && heap[l] < heap[m]) m = l; if (r < n && heap[r] < heap[m]) m = r; if (m === i) break; [heap[m], heap[i]] = [heap[i], heap[m]]; i = m; } };

  for (const x of nums) {
    if (heap.length < k) push(x);
    else if (x > heap[0]) { heap[0] = x; siftDown(0); }
  }
  return heap.sort((a,b) => b - a);
}
```

**Test Cases**

| Input                   | Expected            |
| ----------------------- | ------------------- |
| `[3,2,1,5,6,4], k=2`    | contains `6,5`      |
| `[7,10,4,3,20,15], k=3` | contains `20,15,10` |
| `[1], k=1`              | `[1]`               |

---

## F) Monotonic Stack (Next Greater Element)

**Problem:** For each element, find the next greater element to its right (or `-1`).

**Explanation:** Keep a **decreasing** stack of indices; when current is bigger, pop and set answer.

**Example Input:** `[2,1,2,4,3]`
**Expected Output:** `[4,2,4,-1,-1]`

```ts
export function nextGreater(nums: number[]): number[] {
  const n = nums.length, ans = Array(n).fill(-1);
  const st: number[] = [];
  for (let i = 0; i < n; i++) {
    while (st.length && nums[st[st.length - 1]] < nums[i]) {
      const j = st.pop()!;
      ans[j] = nums[i];
    }
    st.push(i);
  }
  return ans;
}
```

**Test Cases**

| Input         | Expected           |
| ------------- | ------------------ |
| `[2,1,2,4,3]` | `[4,2,4,-1,-1]`    |
| `[1,1,1]`     | `[-1,-1,-1]`       |
| `[5,4,3,2,1]` | `[-1,-1,-1,-1,-1]` |

---

## G) Fast and Slow Pointers (Cycle Detection)

**Problem:** Determine if a linked list has a cycle.

**Explanation:** Move `slow` by 1 and `fast` by 2; if they meet, there is a cycle.

```ts
export class ListNode { constructor(public val = 0, public next: ListNode | null = null){} }

export function hasCycle(head: ListNode | null): boolean {
  let slow = head, fast = head;
  while (fast && fast.next) {
    slow = slow!.next; fast = fast.next.next;
    if (slow === fast) return true;
  }
  return false;
}
```

**Test Cases**: create nodes `a->b->c->d` and set `d.next = b` to expect `true`; set `d.next = null` to expect `false`.

---

## H) How to Reverse a Linked List (Iterative)

**Problem:** Reverse a singly linked list.

**Explanation:** Iterate and rewire `next` pointers.

```ts
export function reverseList(head: ListNode | null): ListNode | null {
  let prev: ListNode | null = null, cur = head;
  while (cur) {
    const nxt = cur.next;
    cur.next = prev;
    prev = cur;
    cur = nxt;
  }
  return prev;
}
```

**Test Cases**: `1->2->3` → `3->2->1` ; `null` → `null`.

---

## I) Level Order Traversal of Binary Tree

**Problem:** Return values level by level.

**Explanation:** BFS with queue; process `size` nodes per level.

```ts
export class TNode { constructor(public val=0, public left: TNode|null=null, public right: TNode|null=null){} }

export function levelOrderTS(root: TNode | null): number[][] {
  if (!root) return [];
  const res: number[][] = [];
  const q: TNode[] = [root];
  while (q.length) {
    const size = q.length, level: number[] = [];
    for (let i = 0; i < size; i++) {
      const node = q.shift()!;
      level.push(node.val);
      if (node.left) q.push(node.left);
      if (node.right) q.push(node.right);
    }
    res.push(level);
  }
  return res;
}
```

**Test Cases**

| Input                     | Expected              |
| ------------------------- | --------------------- |
| `[3,9,20,null,null,15,7]` | `[[3],[9,20],[15,7]]` |
