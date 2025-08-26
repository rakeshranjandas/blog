---
draft: false
tags:
  - dsa
description: .
date: 2025-08-26
---

**Problem link** - https://leetcode.com/problems/maximum-running-time-of-n-computers/description/
```
Given 
- 'n' computers
- 'batteries' array
	
- All 'n' computers are to be run simultaneously by the batteries
- At a time, a battery could power only one computer
- A battery can be unplugged and replugged to any computer any number of times
- Batteries cannot be recharged
  
Find: max running time of computers
```



#### TESTING FOR BINARY SEARCH
Can we apply binary search in any way?
I know it's biased, but if you have solved problems on binary search, you would know that it's better to test forcefully for binary search at first, as determining it would work or not won't take too much time. 

The only thing needed for making sure that binary search can be put is to find monotonicity in some sense. Monotonicity is the property when for an increasing value the output of a function follows a pattern that can direct us during search. 

In this problem, when running time is 0, the computers will work. They keep on working when the running time is increased gradually until it reaches a certain value, say "max", after which the computers won't work anymore. So theres' the monotonicity, and binary search is apt.

#### CHECK FUNCTION

If we could find if the computes could run for any specific running time, we can use binary search over it and quickly solve the problem.

So, the problem that remains is - 
```
Given 
- 'n' computers 
- 'batteries' array
	
Find: Can batteries power the computers for at least "t" minutes?
```


###### A perspective
I want to introduce a certain visualization to aid my explanation as we go further.
```
Imagine a grid arrangement where
- computers c0, c1, c2 ... are rows.
- the minutes are columns.
- the battery have cells which could power one minute.
- the cells of the battery are denoted with a symbol or a character. Different battery have cells marked with different symbol.
      
e.g.
3 computers running for 4 minutes.
The batteries array is [2, 3, 4, 2, 1].
We will see them as [{o,o}, {x,x,x}, {#,#,#,#}, {$,$}, {+}]

A sample configuration looks like:

c[i] m0 m1 m2 m3
c0   o  +  x  x
c1   x  #  o  #
c2   #  $  #  $

```


Some things that can be realised.
###### 1) One battery cannot be simultaneously used for 2 computers.
This is given in the problem statement. What does this mean in our grid?

```
No column should contain more than one occurence of a certain cell

c[i] m0 m1 m2
c0   o  +  x
c1   o  #  o

Above is impossible as battery 'o' is being used for c0 and c1 in the first minute m1.
```


###### 2) One battery cannot be run more than the running time of computers
If we try to use the battery more than the computer's running time, then there should be at least one minute where the same battery type is getting used more than once (Pigeonhole principle). This would violate the above point, that we can't use a battery for multiple computers at any minute.

```

c[i] m0 m1 m2
c0   o  o  o
c1   o  #  +

Here, we are trying to use 4 'o's while the running time of computers is 3.
We would have to put the 4th 'o' in some column that already has that.
Minute m0 has 2 cells of 'o'. This is invalid.

```


###### Forming the solution

Testing if the computers could be run for any given time "t" means we could find certain grid arrangement that doesn't violate the principles above.

We can try for 2 ways to fill the grid 

- **Fill column-wise**, meaning we decide which cells will be used in the first minute, then move on to the second minute, and so on. For this, we have to ensure that that any minute don't repeat cells. This approach seems difficult because 
	(a) you need a different data structure to tell if a cell has been used already in that minute, 
	(b) we would have to devise an algorithm to pick the unique cells for a column, and its difficult to prove if our picking algorithm always will give an arrangement when ideally some arrangement exists.

- **Fill row-wise**, meaning we assign for all the minutes for first computer, then move on to assigning for the second computer, and so on. For this we only need to make sure that for a particular minute 2 computers don't use the same cell. Note that this approach does not suffer from problems faces during previous approach as we do not need to pick unique cells for a row. We can put cells of the same battery in a row, no worries.


We will iterate over the batteries and put the cells row-wise in the grid. When all the cells of a battery gets finished, we will take the cells of the next battery and start placing from the next available place.

```
Say, the batteries are [{o,o,o}, {+,+}, {x,x,x}].
And we are checking whether 3 computers could run for 4 minutes.


After placing the cells of first battery, the grid looks like

c[i] m0 m1 m2 m3
c0   o  o  o  
c1   
c2


We will start placing the cells of next battery from next available place.
If there are no more columns ahead, we will move to the next row and start placing from the first column.

c[i] m0 m1 m2 m3
c0   o  o  o  +
c1   +
c2

After putting the cells of the final battery, it looks like...

c[i] m0 m1 m2 m3
c0   o  o  o  +
c1   +  x  x  x
c2


```


**(Q) How to ensure no column get cells of the same type?**
This can be done by taking at max "t" cells of a battery, meaning cells of a battery should not exceed the number of columns.

```
Say the batteries are [{x,x}, {o,o,o,o,o,o}, {+,+}]
And we are testing whether 2 computers could run for 4 minutes

c[i] m0 m1 m2 m3
c0   x  x  o  o
c1   o  o  +  +

We took 4 cells of "o" even if there are 6 cells available.
Taking more would put more than one cell in a column. (point 2 above)
```


**(Q) When can we say that the computers can never be made to run for "t" minutes?**
This is when we try filling the grid as per above approach and at the end there are empty places. It only means that there aren't sufficient battery cells to power all the computers for that specific time.

```
c[i] m0 m1 m2 m3
c0   o  o  o  +
c1   +  x  x  x
c2

Above grid is incomplete. Hence, 4 minutes is not possible for 3 computers with batteries [3, 2, 3]
```


##### Improvements

We don't need to go through each place of the grid and put the cells one-by-one. 
We can quickly calculate in which column the next available place would be by using the mod(%) operator.

```
Say you are testing for t = 5 
You have put {x,x}, and now looking at m2 to place {o,o,o,o}.
Where would be the next available place?

It will be (2+4)%5 = 1


c[i] m0 m1 m2 m3 m4
c0   x  x  o  o  o
c1   o  _

m1, of row c1, has the next available place.

```



So, the final check function would be us trying to see if the batteries could be used to "n" complete rows. 

**(Q) How do we count the complete rows?**
We will be moving to the next row only when there are no more columns in a rows. So we count the number of times we cross the last column to get the count of complete rows.

The code for check function looks like this.
```
private boolean check(int n, long t, int[] batteries) {
	int completeRows = 0;
	int currentCol = 0;
	
	for(int battery: batteries) {
		currentCol += Math.min((long) battery, t);
		
		if (currentCol >= t) {
			completeRows++;
			
			if (completeRows >= n) {
				return true;
			}
			
			currentCol %= t;
		}
	}
	
	return false;
}
```

*Time complexity of the check function:* O(batteries.length) , since we iterate over each battery at worst.

##### BINARY SEARCH

Once we have the check function ready, we only need to test for different running times and find that value of max running time after which the computers cannot be run anymore.

###### Search range
A running time that definitely works = 0.

A running time that definitely doesn't work is when we try to run the computers more than the total cells available = (sum_of_batteries + 1).

Binary search is to be performed on the range \[0, sum_of_batteries +1\].

*Time complexity of the binary search* = O(log sum_of_batteries)

###### Complexity
*Total time complexity of binary search with check function*	= O(batteries.length * log sum_of_batteries) 

At worst, number of iterations 
	= 1e5 * log (1e5 * 1e9)
	= 1e5 * log (1e14)
	< = 1e5 * 50

*Space complexity*  = O(1), since no extra space is being used.



> [!info]-Full Solution
> ```
> class Solution {  
>     private boolean check(int n, long t, int[] batteries) {  
>         int completeRows = 0;  
>         long currentCol = 0;
> 
>         for(int battery: batteries) {  
>             currentCol += Math.min((long) battery, t);
> 
>             if (currentCol >= t) {  
>                 completeRows++;
> 
>                 if (completeRows >= n) {  
>                     return true;  
>                 }
> 
>                 currentCol %= t;  
>             }  
>         }
> 
>         return false;  
>     }
> 
>     public long maxRunTime(int n, int[] batteries) {  
>         long sumOfBatteries = 0;  
>         for (int battery: batteries) {  
>             sumOfBatteries += battery;  
>         }
> 
>         long lo = 0, hi = sumOfBatteries+1, mid;  
>         while (hi-lo > 1) {  
>             mid = (lo+hi) / 2;
> 
>             if (check(n, mid, batteries)) {  
>                 lo = mid;  
>             } else {  
>                 hi = mid;  
>             }  
>         }
> 
>         return lo;  
>     }  
> }
> ```

