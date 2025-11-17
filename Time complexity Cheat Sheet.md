

---

# ⚔️ DATA STRUCTURES TIME COMPLEXITY CHEAT SHEET
---

## 🔥 **1. Array / List** (Built-in Python `list`, Java `Array`, etc.)

|Operation|Best|Avg|Worst|Notes|
|---|---|---|---|---|
|Access `arr[i]`|O(1)|O(1)|O(1)|Direct index access|
|Insert at end|O(1)|O(1)*|O(n)|*Amortized in dynamic arrays|
|Insert at beginning|O(n)|O(n)|O(n)|All elements shift|
|Insert in middle|O(n)|O(n)|O(n)|Shift required|
|Delete at end|O(1)|O(1)|O(1)|Pop last element|
|Delete at index|O(n)|O(n)|O(n)|Shift needed|
|Search (unsorted)|O(1)|O(n)|O(n)|Linear search|
|Search (sorted)|O(1)|O(log n)|O(log n)|Binary Search only if sorted|

---

## 🔥 **2. Hash Table (Python `dict`, Java `HashMap`)**

|Operation|Best|Avg|Worst|Notes|
|---|---|---|---|---|
|Insert|O(1)|O(1)|O(n)|Rare collisions cause chaining|
|Delete|O(1)|O(1)|O(n)||
|Search|O(1)|O(1)|O(n)|Worst case = all in one bucket|
|Space|—|—|O(n)|More memory due to overhead|

> 💡 **Most-used DS in interviews.** If you're not using `dict` or `set`, you're doing it the hard way.

---

## 🔥 **3. Hash Set (Python `set`, Java `HashSet`)**

Same complexity as hash table but only stores keys.

|Operation|Best|Avg|Worst|Notes|
|---|---|---|---|---|
|Insert|O(1)|O(1)|O(n)||
|Delete|O(1)|O(1)|O(n)||
|Search|O(1)|O(1)|O(n)||

---

## ⚖️ **4. Binary Search Tree (BST)**

|Operation|Best|Avg|Worst|Notes|
|---|---|---|---|---|
|Insert|O(log n)|O(log n)|O(n)|Unbalanced tree = linked list|
|Delete|O(log n)|O(log n)|O(n)||
|Search|O(log n)|O(log n)|O(n)||

> ❗ Use **balanced BSTs** (like AVL, Red-Black) to guarantee `O(log n)` worst case.

---

## 🏛️ **5. Heap (MinHeap/MaxHeap)**

|Operation|Time|Notes|
|---|---|---|
|Insert|O(log n)|Percolate up|
|Delete Min/Max|O(log n)|Remove root, fix heap|
|Search|O(n)|Must scan|
|Peek Min/Max|O(1)|Root is min/max|
|Heapify|O(n)|From unsorted array|

> Used for: Priority Queues, Top K problems, Dijkstra, etc.

---

## 🧱 **6. Stack / Queue**

|Operation|Time|Notes|
|---|---|---|
|Push / Enqueue|O(1)|Stack = push; Queue = enqueue|
|Pop / Dequeue|O(1)||
|Search|O(n)|Must scan|
|Peek|O(1)|Top or front|

> Often used as helpers in recursion, BFS/DFS, etc.

---

## 🪜 **7. Deque (Double-Ended Queue)**

|Operation|Time|Notes|
|---|---|---|
|Add First|O(1)|`deque.appendleft()`|
|Add Last|O(1)|`deque.append()`|
|Remove First|O(1)|`deque.popleft()`|
|Remove Last|O(1)|`deque.pop()`|

> Powerful for sliding window problems.

---

## 🧮 **8. Trie (Prefix Tree)**

|Operation|Time|Notes|
|---|---|---|
|Insert|O(L)|L = length of word|
|Search|O(L)||
|Space|O(n * L)|Can be large if many unique prefixes|

> Used in: Autocomplete, Word Search, Dictionary problems.

---

## 🎢 **9. Graph Representations**

|Representation|Access Edge|Add Vertex|Add Edge|Space|
|---|---|---|---|---|
|Adjacency Matrix|O(1)|O(n^2)|O(1)|O(n^2)|
|Adjacency List|O(k)|O(1)|O(1)|O(n + e)|

> Adjacency List is **preferred** unless graph is very dense.

---

## 🧠 Ranked by How Often You’ll Use Them (Interview Focused):

|Rank|Data Structure|Use Cases|
|---|---|---|
|1️⃣|HashMap / HashSet|Duplicates, frequency counts, lookups|
|2️⃣|Arrays / Lists|Base of everything|
|3️⃣|Heaps|Top-K, Median, Priority Queue|
|4️⃣|Stack / Queue|Recursion, BFS/DFS, Valid Parentheses|
|5️⃣|BST / Set with Order|Tree problems, range queries|
|6️⃣|Trie|Prefix/word problems|
|7️⃣|Deque|Sliding window|
|8️⃣|Graphs|Connectivity, shortest paths, cycles|


---

# 🧠 MOST COMMON ALGORITHMS — TIME COMPLEXITY CHEAT SHEET

---

## 📦 **Sorting Algorithms**

|Algorithm|Best|Avg|Worst|Stable|Notes|
|---|---|---|---|---|---|
|QuickSort|O(n log n)|O(n log n)|O(n²)|❌|In-place, fast in practice|
|MergeSort|O(n log n)|O(n log n)|O(n log n)|✅|Needs extra space|
|HeapSort|O(n log n)|O(n log n)|O(n log n)|❌|Good for max/min sorting|
|BubbleSort|O(n)|O(n²)|O(n²)|✅|Useless except for teaching|
|InsertionSort|O(n)|O(n²)|O(n²)|✅|Good for small arrays|
|TimSort|O(n)|O(n log n)|O(n log n)|✅|Python built-in sort|
|RadixSort|O(nk)|O(nk)|O(nk)|✅|For ints/strings, k = digits/length|

---

## 🕸️ **Graph Algorithms**

|Algorithm|Time|Notes|
|---|---|---|
|BFS (Adj List)|O(V + E)|Shortest path in unweighted graph|
|DFS (Adj List)|O(V + E)|Used for components, cycle detection|
|Dijkstra's (Heap)|O(E + V log V)|Shortest path in weighted graph|
|Bellman-Ford|O(VE)|Handles negative weights|
|Floyd-Warshall|O(V³)|All-pairs shortest path|
|Kruskal's (MST)|O(E log E)|Uses Disjoint Set (Union-Find)|
|Prim's (MST, heap)|O(E + V log V)||
|Topological Sort|O(V + E)|DAG only|
|Tarjan's SCC|O(V + E)|Strongly Connected Components|
|Union-Find (DSU)|O(α(n)) per op|α(n) is inverse Ackermann – very slow-growing|

---

## 🔢 **Search Algorithms**

|Algorithm|Time|Notes|
|---|---|---|
|Binary Search|O(log n)|Needs sorted input|
|Ternary Search|O(log n)|Used for unimodal functions|
|Exponential Search|O(log i)|For unbounded arrays|
|Interpolation Search|O(log log n)|If values are uniformly distributed|

---

## 📊 **Dynamic Programming (Classic)**

|Problem|Time|Notes|
|---|---|---|
|Fibonacci (DP)|O(n)|DP bottom-up or memo|
|0/1 Knapsack|O(nW)|n = items, W = capacity|
|Longest Common Subseq.|O(n*m)||
|Longest Increasing Subseq.|O(n log n)||
|Edit Distance (Levenshtein)|O(n*m)||
|Matrix Chain Multiplication|O(n³)||
|Coin Change|O(n*amount)||
|Palindrome Partitioning|O(n²)||

---

## 🔠 **String Algorithms**

|Algorithm|Time|Notes|
|---|---|---|
|KMP (Pattern Search)|O(n + m)|Preprocess + scan|
|Rabin-Karp|O(n + m) avg|O(nm) worst|
|Trie Insert/Search|O(L)|L = word length|
|Aho-Corasick|O(n + m + z)|Multi-pattern search|
|Suffix Array|O(n log n)|For substring problems|
|Z-Algorithm|O(n)|Pattern matching|
|Rolling Hash (Rabin-Karp base)|O(1) per window|Used in string matching|

---

## 📐 **Math / Number Theory**

|Algorithm|Time|Notes|
|---|---|---|
|Sieve of Eratosthenes|O(n log log n)|Generate primes up to n|
|Euclidean GCD|O(log min(a,b))|Recursive or iterative|
|Fast Exponentiation|O(log n)|Power modulo, `pow(a, b, mod)`|
|Modular Inverse|O(log m)|Extended Euclidean or Fermat|
|Factorial|O(n)||
|Prime Factorization|O(√n)|Trial division|
|Binary to Decimal|O(n)||
|Bit Count (`n & (n-1)`)|O(k)|k = set bits|

---

## 🎲 **Recursion / Backtracking**

|Pattern|Time|Notes|
|---|---|---|
|Subsets (2^n)|O(2ⁿ * n)|n = length of input|
|Permutations|O(n!)|All permutations|
|N-Queens|O(N!)|Brute-force with pruning|
|Sudoku Solver|O(9⁸²)|But prunes heavily|
|Word Search|O(m*n * 4^L)|L = word length|

---

## 📉 **Greedy Algorithms**

|Problem / Pattern|Time|Notes|
|---|---|---|
|Activity Selection|O(n log n)|Sort by end time|
|Huffman Coding|O(n log n)|Priority Queue|
|Fractional Knapsack|O(n log n)|Sort by value/weight|
|Job Scheduling|O(n log n)|Sort + heap|
|Interval Merge|O(n log n)|Sort + scan|

---

## 🧠 Ranked: Which You Should Master First

|Priority|Category|Why|
|---|---|---|
|🔥 #1|Sorting (Quick, Merge, Heap)|Used everywhere|
|🔥 #2|Hashing + Binary Search|Core to 70% of problems|
|🔥 #3|Recursion + Backtracking|Subsets, permutations, DFS problems|
|🔥 #4|DP Basics (Knapsack, LIS)|Every big interview loves DP|
|🔥 #5|Graphs (BFS, DFS, Dijkstra, Topo Sort)|Medium to Hard problems|
|🔥 #6|String (KMP, Trie)|Slightly niche, but asked|
|🔥 #7|Math & Number Theory|For primes, mod, GCD questions|
|🔥 #8|Greedy|Efficient, elegant — fast to code|
|🧠 #9|Advanced DP/Graph (Bellman-Ford, Trie DP, Aho-Corasick)|Competitive / FAANG bar-raiser|

