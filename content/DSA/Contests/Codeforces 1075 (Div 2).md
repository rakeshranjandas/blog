---
draft: false
tags:
  - contest
  - codeforces
date: 2026-01-25
---

### A. [Table with Numbers](https://codeforces.com/contest/2189/problem/A)

#limiting_factor #greedy 

>Observation 1:  We have to pick same number of `x` and `y`. In total we'll be picking `2n` numbers.

>Observation 2:  Assuming `h < l`,  there are more numbers eligible to become `y` than `x`.

Let's say we count all numbers `eligibleForX` and `eligibleForY`. Since `h < l`, the numbers counted as `eligibleForX` will definitely be counted for `eligibleForY`.  From the numbers `eligibleForY` we can only pick half of the numbers to become `y`, the other half expected to become `x`. But also there's a hard limit, `eligibleForX`, on the numbers that could become `x`.

So, the possible pairs that could be formed is determined by the limiting agent, i.e. $min(eligibleForX,\ eligibleForY/2)$`

[Code](https://codeforces.com/contest/2189/submission/359698977)


### B. [The Curse of the Frog](https://codeforces.com/contest/2189/problem/B)

#greedy #constructive

>The frog can jump AT MOST `a`, meaning it can take any jumps from 1 to `a`.  
So, `a` should be really seen as "range". We can reach a target if it lies within the range.  
  
Our task is to minimise rollbacks. So first, we will utilise all attempts without rollbacks.  
Then if we haven't reached the target yet, we have no option but take something with a rollback.

Now of all the options which move with rollback should be picked?  
Remember that if you pick anything, then for that type there won't be any more rollbacks for next `b-1` moves.  
  
The jump with rollback covers a distance of `-c + a`.  
Next `b-1` jumps without rollbacks take you `a(b-1)` distance further.  
Total jump with only one rollback takes you a distance of `-c + a + a(b-1)` = `ab - c`  
  
So whichever jump gets you maximum range with one rollback, you should always pick that one.
That means you can reach the remaining with `ceil(remaining/maxDistanceWithRollback)` rollbacks.

If distance with rollback doesn't move you forward, then you can never reach the target.

[Code](https://codeforces.com/contest/2189/submission/359923472)

### C1. [XOR Convenience (Easy Version)](https://codeforces.com/contest/2189/problem/C1)

#greedy #constructive #xor

>Observation 1: For any two consecutive numbers `2n` and `2n+1`, note that $2n\ \oplus\ (2n+1)\ =\ 1$. This means we can put $a[2n]=2n+1$ and $a[2n+1]=2n$ so that xor of index and the value is 1.

> Observation 2: The XORs at first and last index do not matter. So we can put anything at those indices.

So, we will try to keep 1 somewhere at the right end, and interchange the numbers at the indices so that xor becomes 1. Note that the swap starts with even and made with the next odd number. 
- If `n` is even, it has no forward odd partner. So we put 1 at index n. And interchange for other numbers to the left. The number `n` remains and we'll put that at index 1, and this is no problem due to Observation 2.
- if `n` is odd, we'll first put 1 at index n. Then put `n` at index n-1 so that xor value becomes 1, and this is already present to it's right. At index 1, we can put `n-1` as that's what remains after even-odd interchange is done for other indices.

[Code](https://codeforces.com/contest/2189/submission/359927746)

### C2. [XOR Convenience (Hard Version)](https://codeforces.com/contest/2189/problem/C2)

#greedy #constructive #xor 

In this version, the first index is also considered. So only last index is free for putting any value.

- Why $n=2^x$ can never be solved? Because $n\ \oplus\ i\ >\ n$ , for all i. This means you cannot fit n at any index.

- For odd numbers, C1's solutions is good.

- For even `n`, those who are not of form $2^x$, what could be a possible construction.
	Let's examine the xor with the last number `n`.
	The proposed solution is to off the rightmost set bit of `n`.
	Say `r` is the number that unsets rightmost set bit of `n` after $n\ \oplus\ r$ . 
	
	After applying the process for C1, in index `r` you will be having `r+1`. 
	We will now place `n` at index `r`, and `r+1`(this is what's there in index `r`) at index 1.
	Let's see if this will work out.
	
	At index `r`, there's `n`. The value $n\ \oplus\ r$ will is just `n` with rightmost set bit off. This value is of course greater than `r` and this should be present to the right of `r`. Precisely i should be at the index $(n\ \oplus\ r)+1$, because $n\ \oplus\ r$ is even and in C1's solution we have swapped `2n` and `2n+1` indices with `2n+1` and `2n`. So putting `n` at index `r` seems valid.
	
	At index `1`, there's `r+1`. Now note that $1\ \oplus\ (r+1)\ =\ r$. This is because `r` is even. And `r` is definitely present, precisely at the index `r+1`, because that's how we had in C1's solution.


C1 and C2 are basically the same except one thing. In C1, for numbers of the form $n=2^x$, you can place `n` in index 1, because we are not checking the xor of index 1. But in C2, this will be problematic, as $n\ \oplus\ 1$ could give a bigger number than `n`, and since numbers greater than `n` are not present in the permutation, we can't find answer for such cases.

[Code](https://codeforces.com/contest/2189/submission/360090865)

### D1. [Little String (Easy Version)](https://codeforces.com/contest/2189/problem/D1 "Codeforces Round 1075 (Div. 2)")

#counting #mex

There are 2 criteria's for `x` to be MEX within a certain range `[l, r]`
	1) All numbers from `0` to `x-1` can be found within `[l, r]`
	2) There should not be 'x' within `[l, r]`
``
Another perspective would be that all the numbers from `0` to `x-1` lies to one side of `x`.

With above reasoning, we can see that we cannot find `x` as MEX in any range when of the numbers from `0` to `x-1` some lie to left and some to right of `x` in the permutation.

```

Can find MEX x = 3
	3 (1 0 2) 
	(2 0 1) 3
	
Cannot find MEX x = 3
	(2 1) 3 (0)
	(0) 3 (1 2)
	
```


Let's try generating a valid permutation. Say, we have somehow successfully placed `0` till `x-1` in some order and now trying to find placement for `x`.

 - if  `x` is needed to be MEX of any range, then we need to place `x` at the extreme.
```
Assume the dashes '_' have some number 0 to x-1.

Then we have to place x at extremes for x to be MEX

x _ _ _ _

or

_ _ _ _ x 

Notice that there are 2 ways to place 'x'

```

- if `x` should not be MEX of any range, then we should put `x` somewhere in between the numbers and not the extremes.
```
_ x _ _ _
_ _ x _ _
_ _ _  x _


Notice that there are x numbers (o to x-1).
This means they have x-1 gaps between them.
We can put 'x' in between those x-1 gaps
```


We will now try creating the permutation by placing 0 till n-1. If something needs to appear as MEX then there are 2 ways, and if not then there are x-1 ways.

[Code](https://codeforces.com/contest/2189/submission/360422864)