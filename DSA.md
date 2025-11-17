**🧠 Pratik's DSA Master Plan (With Patterns + Roadmap)**

---

## 🎯 Goal: Crack Product Company Interviews (FAANG-level and beyond)

### ⌚ Time Allocation: ~1–2 hrs/day (flexible)

### 🔧 Stack: Python for DSA (LeetCode + NeetCode)

---

## Week 1: Foundation + Easy Patterns

**Goals:**

- Build Python muscle memory for DSA
    
- Learn key data structures (list, set, dict)
    
- Cover core patterns: Sliding Window, Two Pointers, HashMaps
    

**Topics + Problems:**

- Arrays & Strings: Two Sum, Valid Anagram, Best Time to Buy Stock
    
- Sliding Window: Longest Substring Without Repeating Characters, Min Size Subarray
    
- Two Pointers: Valid Palindrome, Reverse String
    
- HashMaps: Group Anagrams, Top K Frequent Elements
    
- Stack: Valid Parentheses, Min Stack
    

---

## Week 2: Intermediate Patterns + Recursion Basics

**Goals:**

- Deepen Sliding Window + Recursion
    
- Learn DFS/BFS basics
    
- Understand stack/queue use cases
    

**Topics + Problems:**

- Recursion: Factorial, Fibonacci, Subsets
    
- Backtracking Intro: Permutations, N-Queens (Start with 4x4)
    
- BFS: Binary Tree Level Order Traversal
    
- DFS: Max Depth of Binary Tree, Number of Islands
    
- Queue: Implement Stack using Queues, Daily Temperatures
    

---

## Week 3: Binary Search + Linked Lists + Sorting

**Goals:**

- Understand Binary Search and all variants
    
- Practice pointer-heavy problems
    

**Topics + Problems:**

- Binary Search: Search Insert Position, Koko Eating Bananas, Median of Two Sorted Arrays (hard)
    
- Linked List: Reverse Linked List, Detect Cycle, Merge Two Sorted Lists
    
- Sorting: Merge Sort, Quick Sort (manual)
    

---

## Week 4: Advanced Patterns

**Goals:**

- Apply Greedy + Dynamic Programming
    
- Revisit complex problems from earlier
    

**Topics + Problems:**

- Greedy: Jump Game, Gas Station, Candy
    
- DP (1D): Climbing Stairs, House Robber, Fibonacci
    
- DP (2D): Unique Paths, 0/1 Knapsack (basic), Longest Common Subsequence
    

---

### ✅ **Week 5: Advanced Graph Algorithms**

| Algorithm                     | Problem Type                           | Key Problems                                         |
| ----------------------------- | -------------------------------------- | ---------------------------------------------------- |
| **Dijkstra's**                | Single-source shortest path (weighted) | Network Delay Time, Cheapest Flights                 |
| **Bellman-Ford**              | Handles negative weights               | Not on LC often, but important                       |
| **Prim's Algorithm**          | Minimum Spanning Tree (MST)            | Connect Cities with Minimum Cost                     |
| **Kruskal's Algorithm**       | MST via Union-Find                     | Minimum Cost to Connect All Points                   |
| **Topological Sort**          | DAGs, dependency order                 | Course Schedule I/II                                 |
| **Union Find / Disjoint Set** | Cycle detection, components            | Redundant Connection, Number of Connected Components |


| Algorithm                               | Real-World Use                                 | FAANG/DSA Relevance                    | Learn It?                                |
| --------------------------------------- | ---------------------------------------------- | -------------------------------------- | ---------------------------------------- |
| **Floyd-Warshall**                      | All-pairs shortest path (dense graphs)         | _Sometimes_ in contests or hard rounds | ✅ Know the concept, skip memorizing code |
| **Knapsack (0/1)**                      | Dynamic choices with limits (budget, capacity) | **Very common** in DP interviews       | ✅ MUST learn this pattern                |
| **Unbounded Knapsack**                  | Reuse items (coin change, combos)              | Variants show up frequently            | ✅ Yes, 100%                              |
| **Bellman-Ford**                        | Graphs with **negative edges**                 | Rare but conceptually important        | 🤏 Just know it exists and its tradeoffs |
| **Floyd’s Cycle Detection**             | Linked lists, functional graphs                | **Very common** in interviews          | ✅ Yes — easy and powerful                |
| **Subset Sum / Partition Equal Subset** | Core DP pattern                                | Shows up all the time                  | ✅ Yes                                    |

| Classic Algo            | When It Becomes Useful                            |
| ----------------------- | ------------------------------------------------- |
| **0/1 Knapsack**        | Every advanced DP (word break, target sum, etc.)  |
| **Floyd-Warshall**      | When solving dense graph/network-related problems |
| **Bellman-Ford**        | For interviews with edge cases in graphs          |
| **Union-Find**          | Disjoint sets, graph cycle detection              |
| **Topological Sort**    | Task scheduling, dependencies, build systems      |
| **LCS / Edit Distance** | String DP problems (common in interviews)         |

## Month 2+: Mix + Practice Rounds

**Goals:**

- Start solving Mediums consistently
    
- Learn to debug fast and optimize
    

**Approach:**

- NeetCode 75 (complete it)
    
- LeetCode Contests (optional)
    
- Practice 2 Medium + 1 Hard per week
    
- Time yourself (~30 min/problem)
    

---

## Core Patterns Reference (Bookmark)

1. **Sliding Window**
    
2. **Two Pointers**
    
3. **Hash Map / Set**
    
4. **Binary Search**
    
5. **Stack / Queue**
    
6. **DFS / BFS (Graph / Tree Traversals)**
    
7. **Backtracking**
    
8. **Greedy**
    
9. **Dynamic Programming**
    
10. **Union Find / Disjoint Set**
    

---

## Tips:

- Track your problems (make a sheet)
    
- Add notes or markdown after solving
    
- Redo wrong ones after 7 days
    
- Write brute → optimize
    
- Talk your logic out loud (interview sim)



|Problem Variant|Fraction Allowed?|Copies Allowed?|Optimal Approach|Greedy Works?|Complexity (typical)|
|---|---|---|---|---|---|
|**0/1 Knapsack**|❌ No|❌ Only 1/item|**DP** (non-greedy)|❌ No|`O(n*W)`|
|**Fractional Knapsack**|✅ Yes|✅ Unlimited|**Greedy** (by value/weight ratio)|✅ Yes|`O(n log n)` (sort)|
|**Unbounded Knapsack**|❌ No|✅ Unlimited|**DP**|❌ No|`O(n*W)`|
|**Multiple Knapsack**|❌ No|🔢 Limited k copies|**DP** (can binary-optimize)|❌ No|`O(n*W)` (or better with tricks)|




