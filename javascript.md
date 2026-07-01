# JavaScript Cheat Sheet for Algorithms and Competitive Programming

Muc tieu: giup programmer da quen giai thuat bang ngon ngu khac adapt nhanh sang JavaScript de lam LeetCode, interview coding, va cac bai online judge co runtime Node.js.

> TL;DR: Trong LeetCode, uu tien `Number`, `Array`, `Map`, `Set`, function ro rang, tranh mutate input neu khong can. Khi can so nguyen lon hoac modulo lon hon `Number.MAX_SAFE_INTEGER`, dung `BigInt`.

---

## 1. JavaScript Runtime Model Can Nho

### JavaScript trong LeetCode

LeetCode thuong chay JavaScript tren Node.js.

Ban viet:

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

Can nho:

- Khong can doc stdin trong LeetCode.
- Function signature do de bai cung cap.
- Input co the la array, string, linked list, tree node object.
- Output can match exact type.
- Runtime Node.js ho tro ES6+ tot: `let`, `const`, `Map`, `Set`, class, arrow function, destructuring.

### JavaScript trong online judge doc stdin

Neu can doc stdin:

```js
const fs = require("fs");
const input = fs.readFileSync(0, "utf8").trim();

const nums = input.split(/\s+/).map(Number);
```

Neu input co the rong:

```js
const fs = require("fs");
const input = fs.readFileSync(0, "utf8").trim();
if (!input) process.exit(0);
```

Output:

```js
const out = [];
out.push(String(answer));
console.log(out.join("\n"));
```

---

## 2. Variables, Scope, Equality

### `let`, `const`, `var`

Dung:

```js
const n = nums.length; // khong reassign
let ans = 0;           // co reassign
```

Tranh `var` neu khong bi platform cu bat buoc.

Khac biet nhanh:

- `let` / `const`: block scoped.
- `var`: function scoped, co hoisting de gay bug.
- `const` khong cho reassign binding, nhung object/array ben trong van mutate duoc.

```js
const arr = [];
arr.push(1); // OK
// arr = [2]; // Error
```

### Equality

Luon dung:

```js
a === b
a !== b
```

Tranh:

```js
a == b
a != b
```

Vi `==` co type coercion:

```js
0 == false      // true
"" == false     // true
null == undefined // true
```

### Truthy / falsy

Falsy:

```js
false, 0, -0, 0n, "", null, undefined, NaN
```

Can can than:

```js
if (map.get(x)) { ... } // sai neu value la 0
```

Dung:

```js
if (map.has(x)) { ... }
```

---

## 3. Number, BigInt, Integer Pitfalls

### `Number`

JavaScript chi co mot numeric type chinh: `Number`, la floating-point 64-bit IEEE 754.

Safe integer:

```js
Number.MAX_SAFE_INTEGER // 9007199254740991
Number.MIN_SAFE_INTEGER // -9007199254740991
```

Check:

```js
Number.isSafeInteger(x)
Number.isInteger(x)
Number.isNaN(x)
```

### Parse number

```js
Number("123")      // 123
parseInt("123", 10) // 123
parseFloat("1.5") // 1.5
```

Dung radix voi `parseInt`:

```js
parseInt(s, 10)
```

### Floor, ceil, round, truncate

```js
Math.floor(3.9) // 3
Math.ceil(3.1)  // 4
Math.round(3.5) // 4
Math.trunc(-3.9) // -3
Math.floor(-3.1) // -4
```

Neu can integer division nhu C++ voi so duong:

```js
const mid = Math.floor((l + r) / 2);
```

Neu co so am va muon truncate toward zero:

```js
const q = Math.trunc(a / b);
```

### Avoid overflow trong midpoint

Trong JS, overflow integer it gap hon C++ vi `Number` co range lon, nhung safe integer van gioi han. Pattern tot:

```js
const mid = l + Math.floor((r - l) / 2);
```

### `NaN`

```js
NaN === NaN // false
Number.isNaN(NaN) // true
```

### Infinity

```js
Infinity
-Infinity
```

Dung cho min/max:

```js
let best = Infinity;
let worst = -Infinity;
```

### Modulo voi so am

Trong JS, `%` la remainder, khong phai mathematical modulo:

```js
-1 % 5 // -1
```

Dung helper:

```js
const mod = (x, m) => ((x % m) + m) % m;
```

### BigInt

Dung khi value vuot `Number.MAX_SAFE_INTEGER` hoac modulo/multiplication can exact.

```js
const a = 12345678901234567890n;
const b = BigInt("12345678901234567890");
```

Rules:

- Khong mix `Number` va `BigInt` trong arithmetic.
- `1n + 2` error.
- Phai dung `1n + 2n`.
- `Math.*` khong ho tro BigInt.

Modulo BigInt:

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

## 4. String

### Basics

```js
const s = "abc";
s.length      // 3
s[0]          // "a"
s.charAt(0)   // "a"
s.slice(1, 3) // "bc"
s.substring(1, 3) // "bc"
```

Strings immutable:

```js
// s[0] = "x"; // khong mutate duoc
```

Muon sua string, convert sang array:

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
s.indexOf("b")      // -1 neu khong co
s.lastIndexOf("b")
s.toLowerCase()
s.toUpperCase()
s.trim()
s.repeat(3)
```

### Iterate chars

Nhanh va du cho ASCII:

```js
for (let i = 0; i < s.length; i++) {
  const ch = s[i];
}
```

Unicode-aware:

```js
for (const ch of s) {
  // handles code points better than indexing
}
```

### Char code

```js
const code = s.charCodeAt(i); // ASCII/UTF-16 code unit
const a = "a".charCodeAt(0);  // 97
const idx = s.charCodeAt(i) - a;
const ch = String.fromCharCode(97 + idx);
```

### Build string efficiently

Tranh noi chuoi qua nhieu trong loop lon:

```js
const parts = [];
for (const x of arr) parts.push(String(x));
return parts.join("");
```

---

## 5. Array

### Create array

```js
const arr = [];
const arr2 = Array(5);          // sparse array, can than
const zeros = Array(5).fill(0); // [0,0,0,0,0]
```

2D array dung cach nay:

```js
const grid = Array.from({ length: m }, () => Array(n).fill(0));
```

Khong dung:

```js
const bad = Array(m).fill(Array(n).fill(0)); // rows share same reference
```

### Basic operations

```js
arr.length
arr.push(x)      // append
arr.pop()        // remove last
arr.unshift(x)   // add first, O(n)
arr.shift()      // remove first, O(n)
arr.at(-1)       // last element
arr[arr.length - 1]
```

Trong queue, tranh `shift()` voi input lon. Dung pointer:

```js
const q = [];
let head = 0;
q.push(start);
while (head < q.length) {
  const cur = q[head++];
}
```

### Slice, splice

```js
arr.slice(l, r) // copy [l, r), khong mutate
arr.splice(i, count, ...items) // mutate
```

### Iterate array

Fast/clear:

```js
for (let i = 0; i < arr.length; i++) {}
for (const x of arr) {}
```

Can index:

```js
for (let i = 0; i < arr.length; i++) {
  const x = arr[i];
}
```

Tranh `for...in` cho array:

```js
for (const i in arr) {} // i la string key, khong nen dung
```

### Copy array

```js
const b = arr.slice();
const c = [...arr];
```

Shallow copy only:

```js
const grid2 = grid.map(row => row.slice());
```

### Sort

Default sort la lexicographic:

```js
[10, 2, 1].sort() // [1,10,2] as strings
```

Numeric sort:

```js
arr.sort((a, b) => a - b); // ascending
arr.sort((a, b) => b - a); // descending
```

Sort object:

```js
items.sort((a, b) => {
  if (a.score !== b.score) return b.score - a.score;
  return a.id - b.id;
});
```

String sort:

```js
words.sort(); // lexicographic UTF-16
```

### Map/filter/reduce

Useful nhung loop thuong de control performance va debug hon:

```js
const doubled = arr.map(x => x * 2);
const positives = arr.filter(x => x > 0);
const sum = arr.reduce((acc, x) => acc + x, 0);
```

Trong code giai thuat, neu loop phuc tap, dung `for`.

### Find

```js
arr.includes(x)
arr.indexOf(x)
arr.find(x => x > 0)
arr.findIndex(x => x > 0)
```

`includes(NaN)` true neu co `NaN`, `indexOf(NaN)` khong tim duoc.

---

## 6. Object, Map, Set

### Object

Plain object:

```js
const freq = {};
freq[x] = (freq[x] ?? 0) + 1;
```

Can than: key cua object bi convert thanh string.

```js
const obj = {};
obj[1] = "a";
obj["1"] // "a"
```

Object an toan hon cho dictionary:

```js
const dict = Object.create(null);
```

### Map

Dung `Map` cho hash map tong quat.

```js
const map = new Map();
map.set(key, value);
map.get(key);
map.has(key);
map.delete(key);
map.size;
map.clear();
```

Increment frequency:

```js
map.set(x, (map.get(x) ?? 0) + 1);
```

Iterate:

```js
for (const [key, value] of map) {}
for (const key of map.keys()) {}
for (const value of map.values()) {}
```

Map giu insertion order.

### Set

```js
const set = new Set();
set.add(x);
set.has(x);
set.delete(x);
set.size;
set.clear();
```

Unique array:

```js
const unique = [...new Set(arr)];
```

Iterate:

```js
for (const x of set) {}
```

### Key equality trong Map/Set

Map/Set compare object by reference:

```js
const a = [1, 2];
const b = [1, 2];
new Set([a]).has(b) // false
```

Neu can key cho coordinate:

```js
const key = (r, c) => `${r},${c}`;
visited.add(key(r, c));
```

Neu coordinate range known:

```js
const id = r * cols + c;
visited.add(id);
```

---

## 7. Destructuring, Spread, Optional Chaining

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

Can than: spread copy shallow va co overhead neu dung trong loop lon.

### Nullish coalescing

```js
const count = map.get(x) ?? 0;
```

Khac voi `||`:

```js
0 || 10  // 10
0 ?? 10  // 0
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
  while (b !== 0) {
    const t = a % b;
    a = b;
    b = t;
  }
  return Math.abs(a);
}
```

### Function expression

```js
const gcd = function(a, b) {};
const add = (a, b) => a + b;
```

### Class

```js
class DSU {
  constructor(n) {
    this.parent = Array.from({ length: n }, (_, i) => i);
    this.size = Array(n).fill(1);
  }

  find(x) {
    while (this.parent[x] !== x) {
      this.parent[x] = this.parent[this.parent[x]];
      x = this.parent[x];
    }
    return x;
  }

  union(a, b) {
    let ra = this.find(a);
    let rb = this.find(b);
    if (ra === rb) return false;
    if (this.size[ra] < this.size[rb]) [ra, rb] = [rb, ra];
    this.parent[rb] = ra;
    this.size[ra] += this.size[rb];
    return true;
  }
}
```

---

## 9. Complexity Notes for JavaScript

### Common complexity

- `arr.push`, `arr.pop`: amortized O(1).
- `arr.shift`, `arr.unshift`: O(n), avoid for queue.
- `arr.sort`: O(n log n), in-place.
- `arr.slice`: O(k).
- `arr.splice`: O(n).
- `Map.get/set/has`: average O(1).
- `Set.add/has/delete`: average O(1).
- String `slice`: usually O(k) enough to assume copied.

### Performance tips

- Prefer `for` loops in hot paths.
- Avoid repeated `s += ch` for very large string, use array join.
- Avoid `Math.max(...arr)` for huge arrays: spread can exceed call stack/argument limit.
- Avoid recursion if depth can be > 10k in Node.js.
- Use iterative DFS/BFS for deep graph/tree.
- Use typed arrays for large numeric arrays when useful: `Int32Array`, `Float64Array`.

---

## 10. Typed Arrays

Useful for dense numeric arrays, memory predictable.

```js
const dist = new Int32Array(n);
dist.fill(-1);

const seen = new Uint8Array(n); // 0/1 boolean-ish
const values = new Float64Array(n);
```

Common types:

- `Int8Array`, `Uint8Array`
- `Int16Array`, `Uint16Array`
- `Int32Array`, `Uint32Array`
- `Float32Array`, `Float64Array`
- `BigInt64Array`, `BigUint64Array`

Notes:

- Fixed length.
- Default value is 0.
- Methods are array-like but not identical in all details.
- Good for DP, visited, distance, frequency over known integer domain.

---

## 11. Common Helper Functions

### Min/max safely

```js
let mn = Infinity;
let mx = -Infinity;
for (const x of arr) {
  if (x < mn) mn = x;
  if (x > mx) mx = x;
}
```

For small arrays:

```js
Math.min(...arr)
Math.max(...arr)
```

### Sum

```js
let sum = 0;
for (const x of arr) sum += x;
```

### Frequency

```js
function frequency(arr) {
  const map = new Map();
  for (const x of arr) map.set(x, (map.get(x) ?? 0) + 1);
  return map;
}
```

### Count chars lowercase `a-z`

```js
const cnt = Array(26).fill(0);
for (let i = 0; i < s.length; i++) {
  cnt[s.charCodeAt(i) - 97]++;
}
```

### GCD / LCM

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
  return Math.abs(a / gcd(a, b) * b);
}
```

### Fast power modulo Number

Neu multiplication khong vuot safe integer qua nhieu. Voi MOD `1e9+7`, `a * a` co the vuot safe integer neu a gan MOD. Trong JS exact modular multiplication voi MOD lon nen dung BigInt neu can chinh xac tuyet doi.

```js
function modPow(base, exp, mod) {
  let res = 1n;
  let b = BigInt(base) % BigInt(mod);
  let e = BigInt(exp);
  const m = BigInt(mod);
  while (e > 0n) {
    if (e & 1n) res = (res * b) % m;
    b = (b * b) % m;
    e >>= 1n;
  }
  return res;
}
```

### Binary search lower/upper bound

```js
function lowerBound(arr, target) {
  let l = 0;
  let r = arr.length;
  while (l < r) {
    const m = l + Math.floor((r - l) / 2);
    if (arr[m] < target) l = m + 1;
    else r = m;
  }
  return l;
}

function upperBound(arr, target) {
  let l = 0;
  let r = arr.length;
  while (l < r) {
    const m = l + Math.floor((r - l) / 2);
    if (arr[m] <= target) l = m + 1;
    else r = m;
  }
  return l;
}
```

---

## 12. Data Structure Templates

### Stack

```js
const st = [];
st.push(x);
const top = st[st.length - 1];
const x = st.pop();
```

Monotonic increasing stack indices:

```js
const st = [];
for (let i = 0; i < nums.length; i++) {
  while (st.length && nums[st[st.length - 1]] > nums[i]) {
    const j = st.pop();
    // nums[i] is next smaller for j
  }
  st.push(i);
}
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

Simple deque with array + head pointer enough for many problems if only push back and pop front.

For true deque:

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

### Min Heap / Priority Queue

JavaScript khong co built-in heap. Tu implement:

```js
class Heap {
  constructor(compare = (a, b) => a - b) {
    this.a = [];
    this.compare = compare; // < 0 means a has higher priority than b
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
        const l = i * 2 + 1;
        const r = i * 2 + 2;
        if (l < a.length && this.compare(a[l], a[best]) < 0) best = l;
        if (r < a.length && this.compare(a[r], a[best]) < 0) best = r;
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
const pairHeap = new Heap((a, b) => a[0] - b[0]); // by distance
```

### DSU / Union Find

```js
class DSU {
  constructor(n) {
    this.parent = Array.from({ length: n }, (_, i) => i);
    this.rank = Array(n).fill(0);
    this.components = n;
  }

  find(x) {
    if (this.parent[x] !== x) {
      this.parent[x] = this.find(this.parent[x]);
    }
    return this.parent[x];
  }

  union(a, b) {
    let ra = this.find(a);
    let rb = this.find(b);
    if (ra === rb) return false;
    if (this.rank[ra] < this.rank[rb]) [ra, rb] = [rb, ra];
    this.parent[rb] = ra;
    if (this.rank[ra] === this.rank[rb]) this.rank[ra]++;
    this.components--;
    return true;
  }
}
```

Iterative `find` de tranh recursion:

```js
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
```

### Trie

Object/Map-based:

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

Array-based lowercase:

```js
class Node {
  constructor() {
    this.next = Array(26).fill(null);
    this.end = false;
  }
}
```

---

## 13. Linked List Patterns

LeetCode ListNode:

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

### Fast slow pointer

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

## 14. Tree Patterns

LeetCode TreeNode:

```js
function TreeNode(val, left, right) {
  this.val = val ?? 0;
  this.left = left ?? null;
  this.right = right ?? null;
}
```

### DFS recursive

```js
function dfs(node) {
  if (!node) return 0;
  const left = dfs(node.left);
  const right = dfs(node.right);
  return Math.max(left, right) + 1;
}
```

Can than stack overflow neu tree rat deep.

### DFS iterative preorder

```js
const st = [root];
while (st.length) {
  const node = st.pop();
  if (!node) continue;
  // visit node
  st.push(node.right);
  st.push(node.left);
}
```

### Inorder iterative

```js
const res = [];
const st = [];
let cur = root;

while (cur || st.length) {
  while (cur) {
    st.push(cur);
    cur = cur.left;
  }
  cur = st.pop();
  res.push(cur.val);
  cur = cur.right;
}
```

### BFS level order

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

## 15. Graph Patterns

### Build adjacency list

0-indexed:

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

### BFS shortest path unweighted

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

### DFS iterative

```js
const seen = Array(n).fill(false);
const st = [start];
seen[start] = true;

while (st.length) {
  const u = st.pop();
  for (const v of graph[u]) {
    if (seen[v]) continue;
    seen[v] = true;
    st.push(v);
  }
}
```

### Topological sort Kahn

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

  return order.length === n ? order : null; // null means cycle
}
```

### Dijkstra

```js
function dijkstra(n, graph, src) {
  const dist = Array(n).fill(Infinity);
  dist[src] = 0;

  const pq = new Heap((a, b) => a[0] - b[0]); // [dist, node]
  pq.push([0, src]);

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

## 16. Grid Patterns

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
const inside = (r, c) => r >= 0 && r < m && c >= 0 && c < n;
```

### BFS grid

```js
const dist = Array.from({ length: m }, () => Array(n).fill(-1));
const q = [[sr, sc]];
let head = 0;
dist[sr][sc] = 0;

while (head < q.length) {
  const [r, c] = q[head++];
  for (const [dr, dc] of dirs4) {
    const nr = r + dr;
    const nc = c + dc;
    if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;
    if (dist[nr][nc] !== -1) continue;
    dist[nr][nc] = dist[r][c] + 1;
    q.push([nr, nc]);
  }
}
```

### Flatten coordinate

```js
const id = r * n + c;
const r = Math.floor(id / n);
const c = id % n;
```

---

## 17. Binary Search Patterns

### Search exact in sorted array

```js
function binarySearch(arr, target) {
  let l = 0;
  let r = arr.length - 1;
  while (l <= r) {
    const m = l + Math.floor((r - l) / 2);
    if (arr[m] === target) return m;
    if (arr[m] < target) l = m + 1;
    else r = m - 1;
  }
  return -1;
}
```

### First true

Tim index dau tien thoa predicate `ok(i)`.

```js
function firstTrue(n, ok) {
  let l = 0;
  let r = n;
  while (l < r) {
    const m = l + Math.floor((r - l) / 2);
    if (ok(m)) r = m;
    else l = m + 1;
  }
  return l;
}
```

### Binary search answer

```js
let l = minPossible;
let r = maxPossible;
while (l < r) {
  const m = l + Math.floor((r - l) / 2);
  if (can(m)) r = m;
  else l = m + 1;
}
return l;
```

For maximum feasible:

```js
let l = minPossible;
let r = maxPossible;
while (l < r) {
  const m = l + Math.ceil((r - l) / 2);
  if (can(m)) l = m;
  else r = m - 1;
}
return l;
```

---

## 18. Two Pointers and Sliding Window

### Opposite direction two pointers

```js
let l = 0;
let r = arr.length - 1;
while (l < r) {
  const sum = arr[l] + arr[r];
  if (sum === target) return [l, r];
  if (sum < target) l++;
  else r--;
}
```

### Same direction sliding window

For positive numbers / monotonic property:

```js
let l = 0;
let sum = 0;
let best = Infinity;

for (let r = 0; r < nums.length; r++) {
  sum += nums[r];
  while (sum >= target) {
    best = Math.min(best, r - l + 1);
    sum -= nums[l++];
  }
}
```

### Sliding window with frequency

```js
const cnt = new Map();
let l = 0;
let distinct = 0;

for (let r = 0; r < arr.length; r++) {
  const x = arr[r];
  const old = cnt.get(x) ?? 0;
  if (old === 0) distinct++;
  cnt.set(x, old + 1);

  while (distinct > k) {
    const y = arr[l++];
    const next = cnt.get(y) - 1;
    if (next === 0) {
      cnt.delete(y);
      distinct--;
    } else {
      cnt.set(y, next);
    }
  }

  // window [l, r] valid
}
```

---

## 19. Prefix Sum, Difference Array

### Prefix sum

```js
const pref = Array(nums.length + 1).fill(0);
for (let i = 0; i < nums.length; i++) {
  pref[i + 1] = pref[i] + nums[i];
}

// sum [l, r)
const sum = pref[r] - pref[l];
```

### Prefix frequency

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

Range add `[l, r]` inclusive:

```js
const diff = Array(n + 1).fill(0);
for (const [l, r, val] of queries) {
  diff[l] += val;
  if (r + 1 < n) diff[r + 1] -= val;
}

const arr = Array(n).fill(0);
let cur = 0;
for (let i = 0; i < n; i++) {
  cur += diff[i];
  arr[i] = cur;
}
```

---

## 20. Dynamic Programming Patterns

### 1D DP

```js
const dp = Array(n + 1).fill(0);
dp[0] = 1;
for (let i = 1; i <= n; i++) {
  dp[i] = dp[i - 1] + dp[i - 2];
}
```

### 2D DP

```js
const dp = Array.from({ length: m }, () => Array(n).fill(0));
```

### Rolling array

```js
let prev = Array(n).fill(0);
let cur = Array(n).fill(0);

for (let i = 0; i < m; i++) {
  cur.fill(0);
  for (let j = 0; j < n; j++) {
    // cur[j] = ...
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
  // compute

  memo.set(key, ans);
  return ans;
}
```

For small integer states, encode key:

```js
const key = i * M + state;
```

### Knapsack 0/1

```js
const dp = Array(cap + 1).fill(0);
for (const [w, val] of items) {
  for (let c = cap; c >= w; c--) {
    dp[c] = Math.max(dp[c], dp[c - w] + val);
  }
}
```

### Unbounded knapsack

```js
const dp = Array(cap + 1).fill(0);
for (const [w, val] of items) {
  for (let c = w; c <= cap; c++) {
    dp[c] = Math.max(dp[c], dp[c - w] + val);
  }
}
```

---

## 21. Backtracking Patterns

### Basic template

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

### Permutations with used

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

### Handle duplicates

```js
nums.sort((a, b) => a - b);

for (let i = start; i < nums.length; i++) {
  if (i > start && nums[i] === nums[i - 1]) continue;
  // choose nums[i]
}
```

---

## 22. Bit Manipulation

JavaScript bitwise operators convert operands to signed 32-bit integers.

Operators:

```js
a & b
a | b
a ^ b
~a
a << k
a >> k   // signed shift
a >>> k  // unsigned shift
```

Check bit:

```js
if ((mask & (1 << i)) !== 0) {}
```

Set bit:

```js
mask |= 1 << i;
```

Clear bit:

```js
mask &= ~(1 << i);
```

Toggle bit:

```js
mask ^= 1 << i;
```

Lowbit:

```js
const low = mask & -mask;
```

Count bits:

```js
function popcount(x) {
  let cnt = 0;
  while (x !== 0) {
    x &= x - 1;
    cnt++;
  }
  return cnt;
}
```

For masks > 31 bits, use `BigInt`:

```js
let mask = 0n;
mask |= 1n << BigInt(i);
if ((mask & (1n << BigInt(i))) !== 0n) {}
```

---

## 23. Common LeetCode Object Shapes

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

### N-ary tree

```js
function Node(val, children) {
  this.val = val;
  this.children = children ?? [];
}
```

---

## 24. Common Patterns by Problem Type

### Hash map lookup

Use when:

- Need complement lookup.
- Need count/frequency.
- Need first index/last index.
- Need group by key.

```js
const map = new Map();
for (let i = 0; i < nums.length; i++) {
  const x = nums[i];
  if (map.has(target - x)) return [map.get(target - x), i];
  map.set(x, i);
}
```

### Sort + scan

Use when:

- Need merge intervals.
- Need greedy by start/end/cost.
- Need two pointers.

```js
intervals.sort((a, b) => a[0] - b[0]);
const merged = [];
for (const [s, e] of intervals) {
  if (!merged.length || merged[merged.length - 1][1] < s) {
    merged.push([s, e]);
  } else {
    merged[merged.length - 1][1] = Math.max(merged[merged.length - 1][1], e);
  }
}
```

### Monotonic stack

Use when:

- Next greater/smaller element.
- Largest rectangle.
- Remove k digits.
- Contribution by previous/next smaller.

```js
const nextGreater = Array(nums.length).fill(-1);
const st = [];

for (let i = 0; i < nums.length; i++) {
  while (st.length && nums[i] > nums[st[st.length - 1]]) {
    nextGreater[st.pop()] = nums[i];
  }
  st.push(i);
}
```

### Monotonic queue

Sliding window maximum:

```js
const dq = []; // stores indices
let head = 0;
const ans = [];

for (let i = 0; i < nums.length; i++) {
  while (head < dq.length && dq[head] <= i - k) head++;
  while (head < dq.length && nums[dq[dq.length - 1]] <= nums[i]) dq.pop();
  dq.push(i);
  if (i >= k - 1) ans.push(nums[dq[head]]);
}
```

### Greedy

Typical shape:

```js
items.sort((a, b) => a.end - b.end);
let count = 0;
let lastEnd = -Infinity;

for (const item of items) {
  if (item.start >= lastEnd) {
    count++;
    lastEnd = item.end;
  }
}
```

### BFS multi-source

```js
const q = [];
let head = 0;
const dist = Array.from({ length: m }, () => Array(n).fill(-1));

for (const [r, c] of sources) {
  q.push([r, c]);
  dist[r][c] = 0;
}

while (head < q.length) {
  const [r, c] = q[head++];
  // expand
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

## 25. Input Parsing Templates

### All numbers

```js
const fs = require("fs");
const data = fs.readFileSync(0, "utf8").trim().split(/\s+/).map(Number);
let idx = 0;

const n = data[idx++];
const arr = data.slice(idx, idx + n);
idx += n;
```

### Lines

```js
const fs = require("fs");
const lines = fs.readFileSync(0, "utf8").trimEnd().split(/\r?\n/);
```

### Mixed line parsing

```js
const [n, m] = lines[0].split(" ").map(Number);
const grid = [];
for (let i = 1; i <= n; i++) {
  grid.push(lines[i].trim().split(""));
}
```

### Fast scanner style

```js
const fs = require("fs");
const tokens = fs.readFileSync(0, "utf8").trim().split(/\s+/);
let ptr = 0;

const next = () => tokens[ptr++];
const nextNum = () => Number(tokens[ptr++]);
```

---

## 26. Common Bugs When Switching to JavaScript

### 1. Numeric sort bug

```js
arr.sort(); // wrong for numbers
arr.sort((a, b) => a - b); // correct
```

### 2. Queue using `shift()`

```js
q.shift(); // O(n), TLE risk
```

Use head pointer.

### 3. 2D array shared rows

```js
Array(m).fill(Array(n).fill(0)); // wrong
Array.from({ length: m }, () => Array(n).fill(0)); // correct
```

### 4. Missing `Map.has`

```js
if (map.get(key)) {} // wrong if value can be 0/false/""
if (map.has(key)) {} // correct
```

### 5. Object key coercion

```js
obj[[1, 2]] // key becomes "1,2"
```

Use explicit key string or `Map`.

### 6. Recursive DFS stack overflow

Node.js stack can overflow on deep recursion. Use iterative stack when depth large.

### 7. Bitwise 32-bit limitation

```js
1 << 31 // negative
1 << 32 // same as 1 << 0
```

Use `BigInt` for wide masks.

### 8. BigInt mixing

```js
1n + 1 // TypeError
1n + 1n // OK
```

### 9. `Array(n).map(...)` on sparse array

```js
Array(5).map((_, i) => i) // empty slots remain
Array.from({ length: 5 }, (_, i) => i) // [0,1,2,3,4]
```

### 10. Mutating input accidentally

```js
nums.sort((a, b) => a - b); // mutates nums
```

If need preserve:

```js
const sorted = nums.slice().sort((a, b) => a - b);
```

---

## 27. Quick Reference

### Collections

```js
// Array
arr.push(x); arr.pop(); arr.at(-1); arr.length;

// Map
map.set(k, v); map.get(k); map.has(k); map.delete(k); map.size;

// Set
set.add(x); set.has(x); set.delete(x); set.size;

// String
s.length; s[i]; s.slice(l, r); s.charCodeAt(i);
```

### Common one-liners

```js
const n = nums.length;
const last = arr[arr.length - 1];
const copy = arr.slice();
const grid = Array.from({ length: m }, () => Array(n).fill(0));
const freq = new Map();
const key = (r, c) => `${r},${c}`;
const mid = l + Math.floor((r - l) / 2);
```

### Direction arrays

```js
const dirs = [[1,0],[-1,0],[0,1],[0,-1]];
```

### Safe default values

```js
const count = map.get(x) ?? 0;
const value = obj[key] ?? defaultValue;
```

### Sort comparators

```js
arr.sort((a, b) => a - b);
arr.sort((a, b) => b - a);
pairs.sort((a, b) => a[0] - b[0] || a[1] - b[1]);
```

---

## 28. Practice Adaptation Checklist

Khi chuyen solution tu C++/Java/Python sang JavaScript:

1. Replace `unordered_map` / `HashMap` bang `Map`.
2. Replace `unordered_set` / `HashSet` bang `Set`.
3. Replace `vector` bang `Array`.
4. Replace `queue` bang `Array + head pointer`.
5. Implement `Heap` neu can priority queue.
6. Check numeric sort comparator.
7. Check integer overflow / exactness neu co multiplication modulo lon.
8. Avoid recursion neu graph/tree depth lon.
9. Encode composite key ro rang: string key hoac integer id.
10. Dung `??` thay vi `||` khi default value co the la `0`.

---

## 29. Minimal Templates to Memorize

### Queue BFS

```js
const q = [start];
let head = 0;
while (head < q.length) {
  const u = q[head++];
}
```

### Frequency Map

```js
const cnt = new Map();
for (const x of arr) cnt.set(x, (cnt.get(x) ?? 0) + 1);
```

### 2D DP

```js
const dp = Array.from({ length: m }, () => Array(n).fill(0));
```

### Numeric Sort

```js
arr.sort((a, b) => a - b);
```

### Heap Usage

```js
const pq = new Heap((a, b) => a[0] - b[0]);
pq.push([0, start]);
while (!pq.isEmpty()) {
  const [d, u] = pq.pop();
}
```

