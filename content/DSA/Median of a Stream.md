---
draft: false
tags:
  - dsa
description: .
date: 2025-09-11
---


**Problem link** - https://leetcode.com/problems/find-median-from-data-stream/description/

```
Given a list of numbers "a". 
Find all the medians of numbers a[0] to a[i], where "i" is from 0 to length(a)-1. 
```


#### Median

Median gives the idea of what's in the middle. 
It's the middle element when the elements are arranged in sorted manner (doesn't matter increasing or decreasing, since middle is middle)  
 - If there are total odd elements there is a single "middle", and that's the median  
- If there are total even elements there are two "middle" elements. In that case the average of the two middle elements is the median.

```
e.g.  
	For [2, 4, 5, 66, 200, 412, 765], median = 66  
	For [4, 55, 80, 100], median = (55+80)/2 = 135/2 = 67.5  
```

#### Brute-force

One working approach is to always sort everything and then pick the middle index(indices) to find the median, every time a new element comes in.  
The time complexity of that, if there are 'n' numbers in total, would be O($n*nlogn$) = O($n^{100}$)  
It would pose a problem if 'n' is large, say $10^5$. Max computations could be as large as $10^{10}*log(10^5)$, which is infeasible.


#### Optimised

Median is always focused on the "middle". 
How could we easily keep track of the middle elements specifically, when a new element is added to the existing?

We will divide the whole set(S) into 2 subsets - left(L) and right (R).  

<u>Rule 1</u>: L will hold the smaller elements, R will hold the remaining. In other words, largest element in L should not be more than the smallest element in R.
```
S = {20, 10, 40, 30}

L = {20, 10}    {40, 30} = R    is valid

L = {20, 40}    {10, 30} = R    is invalid, since max(L) > min(R)
```

<u>Rule 2</u>: We will always keep L and R balanced, meaning equal sized. If there are odd elements in total, R could hold one extra. 

```
S = {20, 10, 50, 40, 30}

L = {20, 10}    {50, 40, 30} = R

* R holds one more than L
```


How do we easily find the middle element(s)?
We ask what's largest in L, and what's smallest in R.

```
For,
L = {20, 10}    {40, 30} = R

Since there are total even elements, there are 2 middles. They are max(L) 20 and min(R) 30.


For, 
L = {20, 10}   {50, 40, 30} = R

Since there are total odd elements, there's one middle and that would be min(R) 30

```


Let's see what happens when a new element arrives. We will try our best to follow the above 2 rules.

```
L = {20, 10}   {50, 40, 30} = R

New number X = 60 comes.
-----------------------

If we follow Rule 1, we can't add X to L.
	L = {10, 20, 60}    {30, 40, 50} = R   is invalid since max(L) 60 > min(R) 30

We should add it to R.

After adding to R, it looks like
L = {20, 10}   {50, 40, 30, 60} = R

Now, Rule 2 seems broken. We need to balance.

We know something should be removed from R and added to L. But which one should be picekd from R?

We can't pick anything because of Rule 1. To abide by it, we have to pick the smallest from R.

	L = {20, 10}   {50, 40, 30, 60} = R
	Pick min(R) 30. Remove it from R and add it to L
	
	L = {20, 10, 30}   {50, 40, 60} = R

Now it looks like
L = {20, 10, 30}   {50, 40, 60} = R

Getting the median after adding the new number:
	There are even elements,so 2 middles.
	middle_1 = max(L) 30
	middle_2 = min(R) 40
	median = (middle_1 + middle_2) / 2 


Another new number X = 5 came
-----------------------------

Looking at Rule 1, we should add X to L.

L = {20, 10, 30, 5}    {50, 40, 60} = R


While balancing, we see that L is bigger, so something needs to be transferred from L to R. 
Again, Rule 1 tells to pick the highest element from L and transfer.

Pick from L, max(L) = 30. Remove it from L and add to R.

Now, it looks like

L = {20, 10, 5}    {50, 40, 60, 30} = R

Getting the median:
	There are total odd elements, so there's a single middle element.
	median = min(R) 30

```

One observation - if we balance at every step, then only one element at max gets transferred between L and R.

#### Data structures

To store L and R, we need data structures that could help us easily find max(L) and min(R).

For that purpose we could use - 
1. **Priority Queue**  
	L could be a max-heap so that getting max(L) is easy.  
	R could be a min-heap so that getting min(R) is easy.  

2. **TreeSet**  
	L and R both could be TreeSets. There are methods available for finding max and min of TreeSets.  
	
	One of the downsides of TreeSet is that can't contain duplicate elements. 
	We can store indices in TreeSet instead of actual numbers. That takes care of the duplicate elements as  indices are always unique.   
	
	The only pro of using a TreeSet is that it supports deletion of any element, unlike PriorityQueue, which can only remove from the top. We'll see the next problem, that uses this property of TreeSet.


#### Code and Complexity

For PriorityQueue implementation,  

**Time complexity:**   
insert: $O(logn)$  
balance: $O(logn)$  
For "n" elements, total complexity is $O(n*(logn + logn)) = O(n*logn)$


**Space complexity**:  
Heaps take total of $O(n)$ extra space.

> [!info]- Code (PriorityQueue)
> ```
> class MedianFinder {
>    private int count;
>    private PriorityQueue<Integer> L, R;
>    private double median;
>
>    public MedianFinder() {
>        count = 0;
>        
>        // Max-heap L
>        L = new PriorityQueue<Integer>((a, b) -> Integer.compare(b, a)); 
>        
>        // Min-heap R
>        R = new PriorityQueue<Integer>();
>    }
>
>    public void addNum(int num) {
>        count++;
>
>        // Step 1: Add to L or R
>        if (R.isEmpty() || num >= R.peek()) {
>            R.add(num);
>        } else {
>            L.add(num);
>        }
>
>        // Step 2: Balance the heaps
>        if (L.size() > R.size()) {
>            R.add(L.poll());
>        } else if (R.size() > L.size() + 1) {
>            L.add(R.poll());
>        }
>
>        // Step 3: Calculate median
>        if (count % 2 == 1) {
>            median = (double) R.peek();
>        } else {
>            median = ((double) L.peek() + R.peek()) / 2;
>        }
>    }
>
>    public double findMedian() {
>        return median;
>    }
>}
> ```

## Another problem - Sliding window median

**Problem link -** https://leetcode.com/problems/sliding-window-median/description/


```
Given is a list of numbers "a", and a sliding window of size "k" where k > 0 and k < length(a).
Find all the medians of the sliding window.
```

#### Problem with Heaps
When a window slides through an array, on every slide, an element is removed and a new element is added. Logic for insertions and balancing are the same as before, heaps are good with them. The problem is delete, since heaps can remove only what's on the top.

For solving this problem of delete, a TreeSet(binary search tree) can be used. It supports deletion at the cost of $O(logn)$. 

Another thing about TreeSet that should be kept in mind is that it cannot hold duplicate elements. For this, instead of using the 


#### Walkthrough
For the elements present in the window, create L and R subsets in the same way as before, but now containing indices instead of value.

When the window slides, find out in which subset the old index lied, remove from the subset, then balance. Then add the new index to L or R (based on Rule 1) and then balance again.

After balancing, finding the medians is same as before.


#### Code and Complexity

**Time complexity:**   
contains: $O(logn)$  
insert: $O(logn)$  
balance: $O(logn)$  
For "n" elements, total complexity is $O(n*(logn + logn + logn)) = O(n*logn)$


**Space complexity**:  
TreeSets L and R take total of $O(n)$ extra space.


 >[!info]- Code (TreeSet)
> ```
> class Solution {
>     public double[] medianSlidingWindow(int[] nums, int k) {
>         ArrayList<Double> res = new ArrayList<>();
> 	    
>         Comparator<Integer> comparator = (a, b) -> 
> 	        nums[a] == nums[b] 
> 	        ? Integer.compare(a, b) 
> 	        : Integer.compare(nums[a], nums[b]);
>         
>         TreeSet<Integer> L = new TreeSet<>(comparator);
>         TreeSet<Integer> R = new TreeSet<>(comparator);
>         
>         Runnable stepBalance = () -> {
>             if (L.size() > R.size()) {
>                 int maxLIndex = L.last();
>                 L.remove(maxLIndex);
>                 R.add(maxLIndex);
>                 
>             } else if (R.size() > L.size() + 1) {
>                 int minRIndex = R.first();
>                 R.remove(minRIndex);
>                 L.add(minRIndex);
>             }
>         };
>         
>         Consumer<Integer> deleteOldIndex = (oldIndex) -> {
>             if (L.contains(oldIndex)) {
>                 L.remove(oldIndex);
>                 
>             } else {
>                 R.remove(oldIndex);
>             }
>         };
>         
>         Consumer<Integer> addNewIndex = (newIndex) -> {
>             if (R.isEmpty()) {
>                 R.add(newIndex);
>             }
>             
>             int minRIndex = R.first();
>             if (nums[newIndex] >= nums[minRIndex]) {
>                 R.add(newIndex);
>                 
>             } else {
>                 L.add(newIndex);
>             }
>         };
>         
>         Supplier<Double> median = () -> {
>             if (L.size() == R.size()) {
>                 return ((double) nums[L.last()] + nums[R.first()]) / 2;
>             }
>             
>             return (double) nums[R.first()];
>         };
>         
>         for (int i = 0; i < k-1; i++) {
>             addNewIndex.accept(i);
>             stepBalance.run();
>         }
>         
>         for (int i = k-1; i < nums.length; i++) {
>             addNewIndex.accept(i);
>             stepBalance.run();
>             
>             res.add(median.get());
>         
>             deleteOldIndex.accept(i-k+1);
>             stepBalance.run();
>         }
>         
>         return res.stream().mapToDouble(Double::doubleValue).toArray();
>     }
> }
> ```

>[!warning]
>   An important thing to consider for TreeSet comparator.  
>    If the comparator returns 0, then TreeSet detects that the element already exists and doesn't insert anymore.  
>    We want indices to ALWAYS be inserted. Hence, if numbers at two indices are the same, then we tell that the lower index is smaller.