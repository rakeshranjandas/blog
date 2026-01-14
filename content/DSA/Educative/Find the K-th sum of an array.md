---
draft: false
tags:
  - dsa
  - heaps
  - dp
  - bfs
  - dijkstra
description: .
date: 2025-09-14
---
**Problem link** - https://leetcode.com/problems/find-the-k-sum-of-an-array/description/  

1) An easier problem - Kth smallest sum in an array with natural numbers.
2) Original problem - Problematic negative numbers
3) The catch - asking the same question in a different way


```
Given an integer array "nums". 
Consider sums of all possible subsequences.
For a given k, what is the k-th largest sum?

Constraints: 
	1 <= nums.length <= 1e5
	-1e9 <= nums[i] <= 1e9
	1 <= k <= min(2000, 2^n)
```

Remember:
```
- Kth largest: 1st largest(max), 2nd largest(2nd max), ... kth largest(kth max)
- Empty subsequence sum is 0
```

#### An easier problem

We'll first try solving a simpler problem
```
Same problem, but now "nums" is an array of whole numbers.
i.e. 0 <= nums[i] <= 1e9
```


**Brute-force approach** involves generating all subsequences, finding their sum and sort them. Generation of subsequences using bitwise method is $O(2^n)$, and that is infeasible for larger n.

There's also a recursive method of generating the subsequences too. Let me explain that briefly.

```
nums = [a, b, c]

start with empty --> {}
take a --> {a}
	take b --> {a, b}
		take c --> {a, b, c}
		remove c
	remove b
	take c --> {a, c}
	remove c
remove a
take b --> {b}
	take c --> {b, c}
	remove c
remove c
take c --> {c}
remove c
```


**Can we use the recursive method in some smart way so that we can step through the subsequence sums one-by-one in increasing order?**

By using PriorityQueue, let's see how we can explore the subsequences in a controlled manner.

```
nums = [4,2,1,3]

We'll first sort the array.

After sort, nums = [1, 2, 3, 4]

The idea is 
	- we'll take a priority queue (min-heap). Subsequence with lowest sum should be popped next.
	- affer we pop, we'll add 2 more numbers to the priority queue.
		  > Add next element to the popped subsequence
		  > Remove last added element of the popped subsequence and add to that the next element 


Add {}    PQ: {}
 
Popped {}    sum = 0
Add {1}    PQ: {1}

Popped {1}    sum = 1
Add {1, 2} {2}    PQ: {1, 2} {2}

Popped {2}    sum = 2   
Add {2, 3} {3}    PQ: {1, 2} {2, 3} {3}

Popped {3}    sum = 3
Add X    PQ: {1, 2} {2, 3}

Popped {1, 2}    sum = 3
Add {1, 2, 3} {1, 3}    PQ: {2, 3} {1, 2, 3} {1, 3}

Popped {1, 3}    sum = 4
Add X    PQ: {2, 3} {1, 2, 3}

Popped {2, 3}    sum = 5
Add X    PQ: {1, 2, 3}

Popped {1, 2, 3}    sum = 6
Add X    PQ: {}

```


```
Imagine Dijkstra over the tree 

{} -> {1} -> {1, 2} -> {1, 2, 3}
		-> {1, 3}
	-> {2} -> {2, 3}
	-> {3}
	

If we sort, we can rearrange the tree and Dijkstra candidates are reduced

{} -> {1} -> {1, 2} -> {1, 2, 3}
					-> {1, 3}
		  -> {2} -> {2, 3}
				-> {3}
				
Note how each node has at max 2 children

```