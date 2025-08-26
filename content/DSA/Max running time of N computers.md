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
	
- All the 'n' computers are to be run simultaneously by the batteries
- At a time, a battery could power one computer
- A battery could be unplugged and replugged to any computer any number of times
- Batteries cannot be recharged
  
Find: max running time of computers
```



#### TESTING FOR BINARY SEARCH
Can we apply binary search in any way?
I know it's bias, but if you have solved problems on binary search, you would know that it's better to test forcefully for binary search at first, as determining it would work or not won't take too much time.

I could try to check if any specific running time will work.

(Hint) If I need to find 'max' running time, it should mean that if I test for a running time more than the max, then it wont work. Values equal to or lesser than max will definitely work.

#### CHECK FUNCTION
To be able to say whether a specifc running time "t" will work means we could find an arrangement and make the computers run for at least "t" minutes. 

If we could find this, then can use binary search and quickly solve the problem.

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
Imagine 
	- computers c0, c1, c2 ... as rows.
	- the minutes they run as columns.
	- denote cells of batteries with a char. Each cell can give power for 1 minute.

e.g.
3 computers for 4 minutes.
The batteries array is [2, 3, 4, 2, 1].
We will see them as [{o,o}, {x,x,x}, {#,#,#,#}, {$,$}, {+}]

A configuration would look like:

c[i] m0 m1 m2 m3
c0   o  +  x  x
c1   x  #  o  #
c2   #  $  #  $

```

###### 1) One battery cannot be simultaneously used for 2 computers.

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

We are trying to use 4 'o's while the running time of computers is 3.
Minute m0 has 2 cells of 'o'. This is invalid.

```


###### Forming the solution

Testing ifthe computers could be run for any given time "t" means we could find certain grid arrangement that it doesn't violate the principles above.

We can try for 2 ways to fill the grid 
- Fill column-wise, meaning we decide which cells will be assigned for first minute, then move on to second minute, and so on. For this we have to maintain some kind of data structure to ensure that a cell that's once used for the column is not put again.
- Fill row-wise, meaning we assign for all the minutes for first computer, then move on to assigning for the second computer, and so on. For this we only need to make sure that for a particular minute 2 computers don't use the same cell.

I will explain the second way as it's much simpler to understand and code. 

We will iterate over the batteries and put the cells row-wise in the grid. When all the cells of a battery gets finished, we will take the cells of the next battery and start placing from the next available place.

```
Say the batteries are [{o,o,o}, {+,+}, {x,x,x}]
And we are checking whether 3 computes could run for 4 minutes.


After placing the cells of first battery, the grid looks like

c[i] m0 m1 m2 m3
c0   o  o  o  
c1   
c2


We will start placing the cells of next battery from next available place.

c[i] m0 m1 m2 m3
c0   o  o  o  +
c1   +
c2

After putting the cells of next battery.

c[i] m0 m1 m2 m3
c0   o  o  o  +
c1   +  x  x  x
c2


```


**(Q) How to ensure if there a certain column never gets cells of same type?**
This can be done by taking at max "t" cells of a battery.

```
Say the batteries are [{x,x}, {o,o,o,o,o,o}, {+,+}]
And we are testing whether 2 computers could run for 4 minutes

c[i] m0 m1 m2 m3
c0   x  x  o  o
c1   o  o  +  +

We took 4 cells of "o" even if there are 6 cells available.
Taking more would put more than one cell in a column. (point 2 above)
```


**(Q) When do we say that the arrangement is not possible, or that the computers can never be made to run for "t" minutes?**
This is when we try filling the grid as per above approach and at the end there are empty places. It only means that there aren't sufficient battery cells to power all the computers for that specific time.


##### Improvements

We don't need to go through each place of the grid to and put the cells one-by-one. 
We can calculate in which column the next available place would be by using the mod operator.

```
Say you are at m2, and you are testing for t = 5
After placing 4 cells, which column will be you next available place?

It will be (2+4)%5 = 1


c[i] m0 m1 m2 m3 m4
c0   x  x  o  o  o
c1   o  ^

	m1, of c1 row, has the next available place

```



So, the final check function would be us trying to see if the batteries could be used to "n" complete rows.

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

*Time complexity of the check function:* O(batteries.length) , since it goes through each battery at worst.

##### BINARY SEARCH

Once we have the check function ready, we only need to test for different running times.

###### Search range
A running time that definitely works = 0.

A running time that definitely won' t work is when we try to run the computers more than the total cells available = (Sum_of_batteries + 1).

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

