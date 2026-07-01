# JavaScript Cheat Sheet for Algorithms and Competitive Programming

Audience: programmers who already know algorithms in another language and want to adapt quickly to JavaScript for LeetCode, interviews, and Node.js based online judges.

Core idea: JavaScript is productive for algorithm problems once you internalize a few differences: all normal numbers are IEEE-754 `Number`, arrays are dynamic, `Map` and `Set` are the default hash collections, there is no built-in heap, array `sort()` is lexicographic unless given a comparator, and `shift()` is too slow for large queues.

---

## 1. LeetCode and Node.js Runtime

### LeetCode style

LeetCode gives you the function signature. You usually do not read stdin.

```js
var twoSum = function(nums, target) {
  const seen = new Map();

  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (seen.has(need)) return [seen.get(need), i];
    seen.set(nums[i], i);
  }

  return [];
};
```

Useful assumptions:

- You can use modern JavaScript features: `let`, `const`, `Map`, `Set`, classes, arrow functions, destructuring, optional chaining, and nullish coalescing.
- Inputs are already parsed into JavaScript values.
- Linked lists and trees are represented as objects.
- Output type must match exactly what the problem expects.

### Online judge stdin style

Read all input:

```js
const fs = require("fs");
const input = fs.readFileSync(0, "utf8").trim();
```

Parse whitespace-separated numbers:

```js
const data = input.split(/\s+/).map(Number);
let idx = 0;

const n = data[idx++];
const arr = data.slice(idx, idx + n);
idx += n;
```

Handle empty input:

```js
const fs = require("fs");
const input = fs.readFileSync(0, "utf8").trim();
if (!input) process.exit(0);
```

Collect output:

```js
const out = [];
out.push(String(answer));
console.log(out.join("\n"));
```

---

## 2. Variables and Scope

### Prefer `const` and `let`

```js
const n = nums.length; // binding is not reassigned
let ans = 0;           // binding is reassigned
```

Avoid `var` unless a platform forces an older style.

Rules:

- `let` and `const` are block-scoped.
- `var` is function-scoped and can cause accidental bugs.
- `const` prevents reassignment of the binding, not mutation of the object.

```js
const arr = [];
arr.push(1); // allowed
// arr = [2]; // TypeError
```

### Equality

Always prefer strict equality:

```js
a === b
a !== b
```

Avoid loose equality in algorithm code:

```js
0 == false          // true
"" == false         // true
null == undefined   // true
```

### Truthy and falsy

Falsy values:

```js
false, 0, -0, 0n, "", null, undefined, NaN
```

Be careful with map lookups:

```js
if (map.get(key)) {
  // Wrong if the stored value can be 0, false, or "".
}

if (map.has(key)) {
  // Correct existence check.
}
```

---

## 3. Numbers and BigInt

### `Number`

JavaScript's normal numeric type is `Number`, a 64-bit floating point value.

```js
Number.MAX_SAFE_INTEGER // 9007199254740991
Number.MIN_SAFE_INTEGER // -9007199254740991
```

Useful checks:

```js
Number.isInteger(x)
Number.isSafeInteger(x)
Number.isNaN(x)
```

### Parsing numbers

```js
Number("123")          // 123
parseInt("123", 10)    // 123
parseFloat("3.14")     // 3.14
```

Always pass radix to `parseInt`:

```js
parseInt(s, 10)
```

### Rounding

```js
Math.floor(3.9)   // 3
Math.ceil(3.1)    // 4
Math.round(3.5)   // 4
Math.trunc(-3.9)  // -3
Math.floor(-3.1)  // -4
```

Integer division for non-negative values:

```js
const q = Math.floor(a / b);
```

Integer division toward zero:

```js
const q = Math.trunc(a / b);
```

Safe midpoint:

```js
const mid = left + Math.floor((right - left) / 2);
```

### `NaN`

```js
NaN === NaN            // false
Number.isNaN(NaN)      // true
```

### Infinity

```js
let best = Infinity;
let worst = -Infinity;
```

### Modulo with negative numbers

`%` is remainder, not mathematical modulo:

```js
-1 % 5 // -1
```

Use this helper:

```js
const mod = (x, m) => ((x % m) + m) % m;
```

### BigInt

Use `BigInt` when exact integer arithmetic may exceed `Number.MAX_SAFE_INTEGER`.

```js
const a = 12345678901234567890n;
const b = BigInt("12345678901234567890");
```

Rules:

- Do not mix `Number` and `BigInt` in arithmetic.
- `1n + 2` throws.
- Use `1n + 2n`.
- `Math.*` does not work with `BigInt`.

Modulo arithmetic with `BigInt`:

```js
const MOD = 1000000007n;
const ans = (a * b) % MOD;
```

Convert:

```js
BigInt(x)
Number(big)
big.toString()
```

---

## 4. Strings

### Basics

```js
const s = "abc";
s.length       // 3
s[0]           // "a"
s.charAt(0)    // "a"
s.slice(1, 3)  // "bc"
```

Strings are immutable:

```js
const arr = s.split("");
arr[0] = "x";
const t = arr.join("");
```

### Common methods

```js
s.includes("ab")
s.startsWith("a")
s.endsWith("c")
s.indexOf("b")       // -1 if not found
s.lastIndexOf("b")
s.toLowerCase()
s.toUpperCase()
s.trim()
s.repeat(3)
```

### Iterate characters

For ASCII or normal LeetCode strings:

```js
for (let i = 0; i < s.length; i++) {
  const ch = s[i];
}
```

Unicode-aware iteration:

```js
for (const ch of s) {
  // Iterates code points better than indexing.
}
```

### Character codes

```js
const code = s.charCodeAt(i);
const base = "a".charCodeAt(0);
const idx = s.charCodeAt(i) - base;
const ch = String.fromCharCode(base + idx);
```

### Build strings efficiently

```js
const parts = [];
for (const x of arr) parts.push(String(x));
return parts.join("");
```

---

## 5. Arrays

### Create arrays

```js
const arr = [];
const sparse = Array(5);       // sparse slots
const zeros = Array(5).fill(0);
```

Create a 2D array:

```js
const grid = Array.from({ length: rows }, () => Array(cols).fill(0));
```

Avoid shared rows:

```js
const bad = Array(rows).fill(Array(cols).fill(0));
```

### Basic operations

```js
arr.length
arr.push(x)       // append
arr.pop()         // remove last
arr.unshift(x)    // add first, O(n)
arr.shift()       // remove first, O(n)
arr.at(-1)        // last element
arr[arr.length - 1]
```

For queues, avoid `shift()`:

```js
const q = [];
let head = 0;

q.push(start);
while (head < q.length) {
  const cur = q[head++];
}
```

### Slice and splice

```js
arr.slice(l, r)              // returns copy of [l, r)
arr.splice(i, count, ...xs)  // mutates array
```

### Iterate arrays

```js
for (let i = 0; i < arr.length; i++) {
  const x = arr[i];
}

for (const x of arr) {
  // value only
}
```

Avoid `for...in` for arrays because indices are strings and inherited keys can appear.

### Copy arrays

```js
const b = arr.slice();
const c = [...arr];
const grid2 = grid.map(row => row.slice());
```

Copies are shallow.

### Sort

Default sort is lexicographic:

```js
[10, 2, 1].sort() // [1, 10, 2]
```

Numeric sort:

```js
arr.sort((a, b) => a - b); // ascending
arr.sort((a, b) => b - a); // descending
```

Pairs or objects:

```js
pairs.sort((a, b) => a[0] - b[0] || a[1] - b[1]);

items.sort((a, b) => {
  if (a.score !== b.score) return b.score - a.score;
  return a.id - b.id;
});
```

### Map, filter, reduce

```js
const doubled = arr.map(x => x * 2);
const positives = arr.filter(x => x > 0);
const sum = arr.reduce((acc, x) => acc + x, 0);
```

In hot paths, `for` loops are often clearer and faster.

### Search helpers

```js
arr.includes(x)
arr.indexOf(x)
arr.find(x => x > 0)
arr.findIndex(x => x > 0)
```

---

## 6. Object, Map, and Set

### Plain object

```js
const freq = {};
freq[x] = (freq[x] ?? 0) + 1;
```

Object keys are strings or symbols:

```js
const obj = {};
obj[1] = "a";
obj["1"] // "a"
```

Safer dictionary:

```js
const dict = Object.create(null);
```

### Map

Use `Map` as the default hash map.

```js
const map = new Map();
map.set(key, value);
map.get(key);
map.has(key);
map.delete(key);
map.size;
map.clear();
```

Frequency:

```js
map.set(x, (map.get(x) ?? 0) + 1);
```

Iteration:

```js
for (const [key, value] of map) {}
for (const key of map.keys()) {}
for (const value of map.values()) {}
```

`Map` preserves insertion order.

### Set

```js
const set = new Set();
set.add(x);
set.has(x);
set.delete(x);
set.size;
set.clear();
```

Unique values:

```js
const unique = [...new Set(arr)];
```

### Composite keys

Objects and arrays are compared by reference:

```js
const a = [1, 2];
const b = [1, 2];
new Set([a]).has(b) // false
```

Use string or integer encoding:

```js
const key = (r, c) => `${r},${c}`;
const id = r * cols + c;
```

---

## 7. Destructuring and Modern Syntax

### Destructuring

```js
const [a, b] = pair;
const [first, ...rest] = arr;
const { val, next } = node;
```

Swap:

```js
[arr[i], arr[j]] = [arr[j], arr[i]];
```

### Spread

```js
const copy = [...arr];
const merged = [...a, ...b];
const obj2 = { ...obj, x: 1 };
```

Spread is shallow and can be expensive inside large loops.

### Nullish coalescing

```js
const count = map.get(x) ?? 0;
```

Difference from `||`:

```js
0 || 10 // 10
0 ?? 10 // 0
```

### Optional chaining

```js
node?.next?.val
```

---

## 8. Functions and Classes

### Function declaration

```js
function gcd(a, b) {
  a = Math.abs(a);
  b = Math.abs(b);

  while (b !== 0) {
    const t = a % b;
    a = b;
    b = t;
  }

  return a;
}
```

### Arrow function

```js
const add = (a, b) => a + b;
const square = x => x * x;
```

### Class template

```js
class Counter {
  constructor() {
    this.map = new Map();
  }

  add(x) {
    this.map.set(x, (this.map.get(x) ?? 0) + 1);
  }

  get(x) {
    return this.map.get(x) ?? 0;
  }
}
```

---

## 9. Complexity and Performance Notes

Common operations:

- `push`, `pop`: amortized O(1).
- `shift`, `unshift`: O(n).
- `sort`: O(n log n), in-place.
- `slice`: O(k).
- `splice`: O(n).
- `Map.get`, `Map.set`, `Map.has`: average O(1).
- `Set.add`, `Set.has`, `Set.delete`: average O(1).

Performance tips:

- Use `Array + head pointer` for queues.
- Avoid recursion if depth can exceed around 10,000.
- Avoid `Math.max(...arr)` for huge arrays due to argument limits.
- Prefer `for` loops in hot paths.
- Use array `join("")` to build very large strings.
- Use typed arrays for dense numeric data.

---

## 10. Typed Arrays

Typed arrays are useful for dense numeric arrays with known size.

```js
const dist = new Int32Array(n);
dist.fill(-1);

const seen = new Uint8Array(n); // 0 or 1
const values = new Float64Array(n);
```

Common typed arrays:

- `Int8Array`, `Uint8Array`
- `Int16Array`, `Uint16Array`
- `Int32Array`, `Uint32Array`
- `Float32Array`, `Float64Array`
- `BigInt64Array`, `BigUint64Array`

Notes:

- Fixed length.
- Default value is 0.
- Good for `visited`, `distance`, frequency arrays, DP arrays, and large numeric buffers.

---

## 11. Utility Functions

### Min and max without spread

```js
let mn = Infinity;
let mx = -Infinity;

for (const x of arr) {
  if (x < mn) mn = x;
  if (x > mx) mx = x;
}
```

### Sum

```js
let sum = 0;
for (const x of arr) sum += x;
```

### Frequency map

```js
function frequency(arr) {
  const map = new Map();
  for (const x of arr) map.set(x, (map.get(x) ?? 0) + 1);
  return map;
}
```

### Lowercase character count

```js
const cnt = Array(26).fill(0);
for (let i = 0; i < s.length; i++) {
  cnt[s.charCodeAt(i) - 97]++;
}
```

### GCD and LCM

```js
function gcd(a, b) {
  a = Math.abs(a);
  b = Math.abs(b);

  while (b !== 0) {
    const t = a % b;
    a = b;
    b = t;
  }

  return a;
}

function lcm(a, b) {
  return Math.abs((a / gcd(a, b)) * b);
}
```

### BigInt modular power

```js
function modPow(base, exp, mod) {
  let res = 1n;
  let b = BigInt(base) % BigInt(mod);
  let e = BigInt(exp);
  const m = BigInt(mod);

  while (e > 0n) {
    if ((e & 1n) === 1n) res = (res * b) % m;
    b = (b * b) % m;
    e >>= 1n;
  }

  return res;
}
```

---

## 12. Binary Search Helpers

### Exact search

```js
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
  }

  return -1;
}
```

### Lower bound

First index `i` such that `arr[i] >= target`.

```js
function lowerBound(arr, target) {
  let left = 0;
  let right = arr.length;

  while (left < right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] < target) left = mid + 1;
    else right = mid;
  }

  return left;
}
```

### Upper bound

First index `i` such that `arr[i] > target`.

```js
function upperBound(arr, target) {
  let left = 0;
  let right = arr.length;

  while (left < right) {
    const mid = left + Math.floor((right - left) / 2);
    if (arr[mid] <= target) left = mid + 1;
    else right = mid;
  }

  return left;
}
```

### Binary search on answer

Minimum feasible value:

```js
let left = minPossible;
let right = maxPossible;

while (left < right) {
  const mid = left + Math.floor((right - left) / 2);
  if (can(mid)) right = mid;
  else left = mid + 1;
}

return left;
```

Maximum feasible value:

```js
let left = minPossible;
let right = maxPossible;

while (left < right) {
  const mid = left + Math.ceil((right - left) / 2);
  if (can(mid)) left = mid;
  else right = mid - 1;
}

return left;
```

---

## 13. Stack, Queue, and Deque

### Stack

```js
const stack = [];
stack.push(x);
const top = stack[stack.length - 1];
const x = stack.pop();
```

### Queue

```js
const q = [];
let head = 0;

q.push(start);
while (head < q.length) {
  const cur = q[head++];
}
```

### Deque

```js
class Deque {
  constructor() {
    this.data = new Map();
    this.head = 0;
    this.tail = 0;
  }

  pushBack(x) {
    this.data.set(this.tail++, x);
  }

  pushFront(x) {
    this.data.set(--this.head, x);
  }

  popFront() {
    if (this.isEmpty()) return undefined;
    const x = this.data.get(this.head);
    this.data.delete(this.head++);
    return x;
  }

  popBack() {
    if (this.isEmpty()) return undefined;
    const x = this.data.get(--this.tail);
    this.data.delete(this.tail);
    return x;
  }

  front() {
    return this.data.get(this.head);
  }

  back() {
    return this.data.get(this.tail - 1);
  }

  size() {
    return this.tail - this.head;
  }

  isEmpty() {
    return this.size() === 0;
  }
}
```

---

## 14. Heap / Priority Queue

JavaScript has no built-in priority queue.

```js
class Heap {
  constructor(compare = (a, b) => a - b) {
    this.a = [];
    this.compare = compare;
  }

  size() {
    return this.a.length;
  }

  isEmpty() {
    return this.a.length === 0;
  }

  peek() {
    return this.a[0];
  }

  push(x) {
    const a = this.a;
    a.push(x);

    let i = a.length - 1;
    while (i > 0) {
      const p = (i - 1) >> 1;
      if (this.compare(a[p], a[i]) <= 0) break;
      [a[p], a[i]] = [a[i], a[p]];
      i = p;
    }
  }

  pop() {
    const a = this.a;
    if (a.length === 0) return undefined;

    const root = a[0];
    const last = a.pop();

    if (a.length > 0) {
      a[0] = last;
      let i = 0;

      while (true) {
        let best = i;
        const left = i * 2 + 1;
        const right = i * 2 + 2;

        if (left < a.length && this.compare(a[left], a[best]) < 0) best = left;
        if (right < a.length && this.compare(a[right], a[best]) < 0) best = right;
        if (best === i) break;

        [a[i], a[best]] = [a[best], a[i]];
        i = best;
      }
    }

    return root;
  }
}
```

Usage:

```js
const minHeap = new Heap((a, b) => a - b);
const maxHeap = new Heap((a, b) => b - a);
const pq = new Heap((a, b) => a[0] - b[0]); // pair heap by first field
```

---

## 15. Union Find / DSU

```js
class DSU {
  constructor(n) {
    this.parent = Array.from({ length: n }, (_, i) => i);
    this.size = Array(n).fill(1);
    this.components = n;
  }

  find(x) {
    let root = x;
    while (this.parent[root] !== root) root = this.parent[root];

    while (this.parent[x] !== x) {
      const p = this.parent[x];
      this.parent[x] = root;
      x = p;
    }

    return root;
  }

  union(a, b) {
    let ra = this.find(a);
    let rb = this.find(b);
    if (ra === rb) return false;

    if (this.size[ra] < this.size[rb]) [ra, rb] = [rb, ra];
    this.parent[rb] = ra;
    this.size[ra] += this.size[rb];
    this.components--;
    return true;
  }
}
```

---

## 16. Trie

### Map-based trie

```js
class TrieNode {
  constructor() {
    this.children = new Map();
    this.end = false;
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  insert(word) {
    let node = this.root;

    for (const ch of word) {
      if (!node.children.has(ch)) node.children.set(ch, new TrieNode());
      node = node.children.get(ch);
    }

    node.end = true;
  }

  search(word) {
    let node = this.root;

    for (const ch of word) {
      if (!node.children.has(ch)) return false;
      node = node.children.get(ch);
    }

    return node.end;
  }
}
```

### Array-based lowercase trie node

```js
class Node {
  constructor() {
    this.next = Array(26).fill(null);
    this.end = false;
  }
}
```

---

## 17. Linked List Patterns

LeetCode shape:

```js
function ListNode(val, next) {
  this.val = val ?? 0;
  this.next = next ?? null;
}
```

### Dummy head

```js
const dummy = new ListNode(0);
let cur = dummy;

for (const x of values) {
  cur.next = new ListNode(x);
  cur = cur.next;
}

return dummy.next;
```

### Reverse linked list

```js
function reverseList(head) {
  let prev = null;
  let cur = head;

  while (cur) {
    const next = cur.next;
    cur.next = prev;
    prev = cur;
    cur = next;
  }

  return prev;
}
```

### Fast and slow pointers

```js
let slow = head;
let fast = head;

while (fast && fast.next) {
  slow = slow.next;
  fast = fast.next.next;
}
```

### Detect cycle

```js
function hasCycle(head) {
  let slow = head;
  let fast = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) return true;
  }

  return false;
}
```

---

## 18. Tree Patterns

LeetCode shape:

```js
function TreeNode(val, left, right) {
  this.val = val ?? 0;
  this.left = left ?? null;
  this.right = right ?? null;
}
```

### Recursive DFS

```js
function height(node) {
  if (!node) return 0;
  return Math.max(height(node.left), height(node.right)) + 1;
}
```

Use iterative DFS if the tree can be very deep.

### Iterative preorder DFS

```js
const stack = [];
if (root) stack.push(root);

while (stack.length) {
  const node = stack.pop();
  // visit node
  if (node.right) stack.push(node.right);
  if (node.left) stack.push(node.left);
}
```

### Iterative inorder traversal

```js
const res = [];
const stack = [];
let cur = root;

while (cur || stack.length) {
  while (cur) {
    stack.push(cur);
    cur = cur.left;
  }

  cur = stack.pop();
  res.push(cur.val);
  cur = cur.right;
}
```

### Level order traversal

```js
const res = [];
const q = [];
let head = 0;

if (root) q.push(root);

while (head < q.length) {
  const size = q.length - head;
  const level = [];

  for (let i = 0; i < size; i++) {
    const node = q[head++];
    level.push(node.val);
    if (node.left) q.push(node.left);
    if (node.right) q.push(node.right);
  }

  res.push(level);
}
```

---

## 19. Graph Patterns

### Build adjacency list

Unweighted:

```js
const graph = Array.from({ length: n }, () => []);

for (const [u, v] of edges) {
  graph[u].push(v);
  graph[v].push(u);
}
```

Weighted:

```js
const graph = Array.from({ length: n }, () => []);

for (const [u, v, w] of edges) {
  graph[u].push([v, w]);
  graph[v].push([u, w]);
}
```

### BFS shortest path in unweighted graph

```js
const dist = Array(n).fill(-1);
const q = [start];
let head = 0;
dist[start] = 0;

while (head < q.length) {
  const u = q[head++];

  for (const v of graph[u]) {
    if (dist[v] !== -1) continue;
    dist[v] = dist[u] + 1;
    q.push(v);
  }
}
```

### Iterative DFS

```js
const seen = Array(n).fill(false);
const stack = [start];
seen[start] = true;

while (stack.length) {
  const u = stack.pop();

  for (const v of graph[u]) {
    if (seen[v]) continue;
    seen[v] = true;
    stack.push(v);
  }
}
```

### Topological sort

```js
function topoSort(n, edges) {
  const graph = Array.from({ length: n }, () => []);
  const indeg = Array(n).fill(0);

  for (const [u, v] of edges) {
    graph[u].push(v);
    indeg[v]++;
  }

  const q = [];
  let head = 0;

  for (let i = 0; i < n; i++) {
    if (indeg[i] === 0) q.push(i);
  }

  const order = [];

  while (head < q.length) {
    const u = q[head++];
    order.push(u);

    for (const v of graph[u]) {
      indeg[v]--;
      if (indeg[v] === 0) q.push(v);
    }
  }

  return order.length === n ? order : null;
}
```

### Dijkstra

```js
function dijkstra(n, graph, source) {
  const dist = Array(n).fill(Infinity);
  dist[source] = 0;

  const pq = new Heap((a, b) => a[0] - b[0]); // [distance, node]
  pq.push([0, source]);

  while (!pq.isEmpty()) {
    const [d, u] = pq.pop();
    if (d !== dist[u]) continue;

    for (const [v, w] of graph[u]) {
      const nd = d + w;
      if (nd < dist[v]) {
        dist[v] = nd;
        pq.push([nd, v]);
      }
    }
  }

  return dist;
}
```

---

## 20. Grid Patterns

### Directions

```js
const dirs4 = [[1, 0], [-1, 0], [0, 1], [0, -1]];

const dirs8 = [
  [1, 0], [-1, 0], [0, 1], [0, -1],
  [1, 1], [1, -1], [-1, 1], [-1, -1],
];
```

### Bounds

```js
const inside = (r, c) => r >= 0 && r < rows && c >= 0 && c < cols;
```

### Grid BFS

```js
const dist = Array.from({ length: rows }, () => Array(cols).fill(-1));
const q = [[sr, sc]];
let head = 0;
dist[sr][sc] = 0;

while (head < q.length) {
  const [r, c] = q[head++];

  for (const [dr, dc] of dirs4) {
    const nr = r + dr;
    const nc = c + dc;

    if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) continue;
    if (dist[nr][nc] !== -1) continue;

    dist[nr][nc] = dist[r][c] + 1;
    q.push([nr, nc]);
  }
}
```

### Flatten coordinates

```js
const id = r * cols + c;
const r = Math.floor(id / cols);
const c = id % cols;
```

---

## 21. Two Pointers and Sliding Window

### Opposite-direction two pointers

```js
let left = 0;
let right = arr.length - 1;

while (left < right) {
  const sum = arr[left] + arr[right];
  if (sum === target) return [left, right];
  if (sum < target) left++;
  else right--;
}
```

### Sliding window with sum

For positive numbers or any monotonic window property:

```js
let left = 0;
let sum = 0;
let best = Infinity;

for (let right = 0; right < nums.length; right++) {
  sum += nums[right];

  while (sum >= target) {
    best = Math.min(best, right - left + 1);
    sum -= nums[left++];
  }
}
```

### Sliding window with frequency

```js
const count = new Map();
let left = 0;
let distinct = 0;

for (let right = 0; right < arr.length; right++) {
  const x = arr[right];
  const old = count.get(x) ?? 0;
  if (old === 0) distinct++;
  count.set(x, old + 1);

  while (distinct > k) {
    const y = arr[left++];
    const next = count.get(y) - 1;

    if (next === 0) {
      count.delete(y);
      distinct--;
    } else {
      count.set(y, next);
    }
  }

  // Window [left, right] is valid.
}
```

---

## 22. Prefix Sum and Difference Array

### Prefix sum

```js
const pref = Array(nums.length + 1).fill(0);

for (let i = 0; i < nums.length; i++) {
  pref[i + 1] = pref[i] + nums[i];
}

// Sum on [left, right)
const sum = pref[right] - pref[left];
```

### Prefix sum with hash map

Count subarrays with sum `k`:

```js
const count = new Map();
count.set(0, 1);

let pref = 0;
let ans = 0;

for (const x of nums) {
  pref += x;
  ans += count.get(pref - k) ?? 0;
  count.set(pref, (count.get(pref) ?? 0) + 1);
}
```

### Difference array

Range add on inclusive `[left, right]`:

```js
const diff = Array(n + 1).fill(0);

for (const [left, right, value] of queries) {
  diff[left] += value;
  if (right + 1 < n) diff[right + 1] -= value;
}

const arr = Array(n).fill(0);
let cur = 0;

for (let i = 0; i < n; i++) {
  cur += diff[i];
  arr[i] = cur;
}
```

---

## 23. Dynamic Programming Patterns

### 1D DP

```js
const dp = Array(n + 1).fill(0);
dp[0] = 1;

for (let i = 1; i <= n; i++) {
  dp[i] += dp[i - 1];
}
```

### 2D DP

```js
const dp = Array.from({ length: rows }, () => Array(cols).fill(0));
```

### Rolling array

```js
let prev = Array(cols).fill(0);
let cur = Array(cols).fill(0);

for (let r = 0; r < rows; r++) {
  cur.fill(0);

  for (let c = 0; c < cols; c++) {
    // cur[c] = ...
  }

  [prev, cur] = [cur, prev];
}
```

### Memoization

```js
const memo = new Map();

function dfs(i, state) {
  const key = `${i}|${state}`;
  if (memo.has(key)) return memo.get(key);

  let ans = 0;
  // compute ans

  memo.set(key, ans);
  return ans;
}
```

Integer key encoding:

```js
const key = i * maxState + state;
```

### 0/1 knapsack

```js
const dp = Array(capacity + 1).fill(0);

for (const [weight, value] of items) {
  for (let c = capacity; c >= weight; c--) {
    dp[c] = Math.max(dp[c], dp[c - weight] + value);
  }
}
```

### Unbounded knapsack

```js
const dp = Array(capacity + 1).fill(0);

for (const [weight, value] of items) {
  for (let c = weight; c <= capacity; c++) {
    dp[c] = Math.max(dp[c], dp[c - weight] + value);
  }
}
```

---

## 24. Backtracking Patterns

### Subsets

```js
const res = [];
const path = [];

function backtrack(start) {
  res.push(path.slice());

  for (let i = start; i < nums.length; i++) {
    path.push(nums[i]);
    backtrack(i + 1);
    path.pop();
  }
}

backtrack(0);
```

### Permutations

```js
const res = [];
const path = [];
const used = Array(nums.length).fill(false);

function dfs() {
  if (path.length === nums.length) {
    res.push(path.slice());
    return;
  }

  for (let i = 0; i < nums.length; i++) {
    if (used[i]) continue;

    used[i] = true;
    path.push(nums[i]);
    dfs();
    path.pop();
    used[i] = false;
  }
}
```

### Skip duplicates

```js
nums.sort((a, b) => a - b);

for (let i = start; i < nums.length; i++) {
  if (i > start && nums[i] === nums[i - 1]) continue;
  // choose nums[i]
}
```

---

## 25. Bit Manipulation

JavaScript bitwise operators convert operands to signed 32-bit integers.

```js
a & b
a | b
a ^ b
~a
a << k
a >> k
a >>> k
```

Common operations:

```js
if ((mask & (1 << i)) !== 0) {}

mask |= 1 << i;      // set
mask &= ~(1 << i);   // clear
mask ^= 1 << i;      // toggle

const lowbit = mask & -mask;
```

Popcount:

```js
function popcount(x) {
  let count = 0;

  while (x !== 0) {
    x &= x - 1;
    count++;
  }

  return count;
}
```

For more than 31 usable bits, use `BigInt`:

```js
let mask = 0n;
mask |= 1n << BigInt(i);

if ((mask & (1n << BigInt(i))) !== 0n) {
  // bit is set
}
```

---

## 26. Monotonic Data Structures

### Monotonic stack: next greater element

```js
const nextGreater = Array(nums.length).fill(-1);
const stack = []; // indices

for (let i = 0; i < nums.length; i++) {
  while (stack.length && nums[i] > nums[stack[stack.length - 1]]) {
    nextGreater[stack.pop()] = nums[i];
  }

  stack.push(i);
}
```

### Monotonic increasing stack

```js
const stack = [];

for (let i = 0; i < nums.length; i++) {
  while (stack.length && nums[stack[stack.length - 1]] > nums[i]) {
    const j = stack.pop();
    // nums[i] is the next smaller value for index j
  }

  stack.push(i);
}
```

### Sliding window maximum

```js
const deque = []; // stores indices
let head = 0;
const ans = [];

for (let i = 0; i < nums.length; i++) {
  while (head < deque.length && deque[head] <= i - k) head++;

  while (
    head < deque.length &&
    nums[deque[deque.length - 1]] <= nums[i]
  ) {
    deque.pop();
  }

  deque.push(i);

  if (i >= k - 1) ans.push(nums[deque[head]]);
}
```

---

## 27. Common LeetCode Object Shapes

### ListNode

```js
function ListNode(val, next) {
  this.val = val === undefined ? 0 : val;
  this.next = next === undefined ? null : next;
}
```

### TreeNode

```js
function TreeNode(val, left, right) {
  this.val = val === undefined ? 0 : val;
  this.left = left === undefined ? null : left;
  this.right = right === undefined ? null : right;
}
```

### Node with random pointer

```js
function Node(val, next, random) {
  this.val = val;
  this.next = next ?? null;
  this.random = random ?? null;
}
```

### N-ary tree node

```js
function Node(val, children) {
  this.val = val;
  this.children = children ?? [];
}
```

---

## 28. Input Parsing Templates

### All tokens as numbers

```js
const fs = require("fs");
const data = fs.readFileSync(0, "utf8").trim().split(/\s+/).map(Number);
let idx = 0;

const n = data[idx++];
const arr = data.slice(idx, idx + n);
idx += n;
```

### Scanner-style parser

```js
const fs = require("fs");
const tokens = fs.readFileSync(0, "utf8").trim().split(/\s+/);
let ptr = 0;

const next = () => tokens[ptr++];
const nextNum = () => Number(tokens[ptr++]);
```

### Line-based parser

```js
const fs = require("fs");
const lines = fs.readFileSync(0, "utf8").trimEnd().split(/\r?\n/);

const [n, m] = lines[0].split(" ").map(Number);
const grid = [];

for (let i = 1; i <= n; i++) {
  grid.push(lines[i].trim().split(""));
}
```

---

## 29. Common JavaScript Pitfalls

### Numeric sort

```js
arr.sort();              // wrong for numbers
arr.sort((a, b) => a - b); // correct
```

### Queue with `shift()`

```js
q.shift(); // O(n), can TLE
```

Use `head` pointer instead.

### Shared rows in 2D arrays

```js
Array(rows).fill(Array(cols).fill(0)); // wrong
Array.from({ length: rows }, () => Array(cols).fill(0)); // correct
```

### `Map.get` as existence check

```js
if (map.get(key)) {} // wrong if value can be falsy
if (map.has(key)) {} // correct
```

### Sparse array mapping

```js
Array(5).map((_, i) => i) // not what you want
Array.from({ length: 5 }, (_, i) => i) // [0, 1, 2, 3, 4]
```

### Bitwise 32-bit limitation

```js
1 << 31 // negative
1 << 32 // same as 1 << 0
```

Use `BigInt` for wide masks.

### BigInt mixing

```js
1n + 1  // TypeError
1n + 1n // OK
```

### In-place mutation

```js
nums.sort((a, b) => a - b); // mutates nums
```

Copy first if needed:

```js
const sorted = nums.slice().sort((a, b) => a - b);
```

### Recursion depth

Deep recursion can overflow the Node.js call stack. Convert DFS to iterative stack when depth is large.

---

## 30. Problem-Type Recipes

### Hash map lookup

Use for complements, frequency, first index, last index, and grouping.

```js
const seen = new Map();

for (let i = 0; i < nums.length; i++) {
  const need = target - nums[i];
  if (seen.has(need)) return [seen.get(need), i];
  seen.set(nums[i], i);
}
```

### Merge intervals

```js
intervals.sort((a, b) => a[0] - b[0]);

const merged = [];

for (const [start, end] of intervals) {
  if (!merged.length || merged[merged.length - 1][1] < start) {
    merged.push([start, end]);
  } else {
    merged[merged.length - 1][1] = Math.max(merged[merged.length - 1][1], end);
  }
}
```

### Multi-source BFS

```js
const q = [];
let head = 0;
const dist = Array.from({ length: rows }, () => Array(cols).fill(-1));

for (const [r, c] of sources) {
  q.push([r, c]);
  dist[r][c] = 0;
}

while (head < q.length) {
  const [r, c] = q[head++];
  // expand neighbors
}
```

### State compression DP

```js
const total = 1 << n;
const dp = Array(total).fill(Infinity);
dp[0] = 0;

for (let mask = 0; mask < total; mask++) {
  for (let i = 0; i < n; i++) {
    if ((mask & (1 << i)) !== 0) continue;

    const next = mask | (1 << i);
    dp[next] = Math.min(dp[next], dp[mask] + cost(mask, i));
  }
}
```

---

## 31. Quick Reference

### Collections

```js
// Array
arr.push(x);
arr.pop();
arr.at(-1);
arr.length;

// Map
map.set(k, v);
map.get(k);
map.has(k);
map.delete(k);
map.size;

// Set
set.add(x);
set.has(x);
set.delete(x);
set.size;

// String
s.length;
s[i];
s.slice(l, r);
s.charCodeAt(i);
```

### Common one-liners

```js
const n = nums.length;
const last = arr[arr.length - 1];
const copy = arr.slice();
const grid = Array.from({ length: rows }, () => Array(cols).fill(0));
const key = (r, c) => `${r},${c}`;
const mid = left + Math.floor((right - left) / 2);
```

### Sort comparators

```js
arr.sort((a, b) => a - b);
arr.sort((a, b) => b - a);
pairs.sort((a, b) => a[0] - b[0] || a[1] - b[1]);
```

### Direction arrays

```js
const dirs4 = [[1, 0], [-1, 0], [0, 1], [0, -1]];
```

### Safe default value

```js
const count = map.get(x) ?? 0;
```

---

## 32. Adaptation Checklist from Other Languages

1. Replace `vector` / `ArrayList` with `Array`.
2. Replace `unordered_map` / `HashMap` with `Map`.
3. Replace `unordered_set` / `HashSet` with `Set`.
4. Replace `queue` with `Array + head pointer`.
5. Implement a heap when you need a priority queue.
6. Always pass a numeric comparator to sort numbers.
7. Use `BigInt` when exact arithmetic exceeds safe integer range.
8. Avoid deep recursion when input can be large.
9. Encode composite keys explicitly.
10. Use `??` instead of `||` when `0` is a valid value.
11. Avoid `shift()` and `unshift()` in hot paths.
12. Build 2D arrays with `Array.from`.

---

## 33. Minimal Templates to Memorize

### Queue BFS

```js
const q = [start];
let head = 0;

while (head < q.length) {
  const u = q[head++];
}
```

### Frequency map

```js
const count = new Map();
for (const x of arr) count.set(x, (count.get(x) ?? 0) + 1);
```

### 2D array

```js
const grid = Array.from({ length: rows }, () => Array(cols).fill(0));
```

### Numeric sort

```js
arr.sort((a, b) => a - b);
```

### Heap usage

```js
const pq = new Heap((a, b) => a[0] - b[0]);
pq.push([0, start]);

while (!pq.isEmpty()) {
  const [d, u] = pq.pop();
}
```
