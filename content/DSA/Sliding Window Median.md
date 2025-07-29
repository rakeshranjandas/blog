
```
>> Present as a story <<


What is a median?
 - standard definition
 - standard way


How heap can be used to find median of a stream?


What's the problem in sliding window?


How we can solve the problem?



```

```
What about this

- Definition 1: middle of sorted
- Brute force

- Problem of brute force

- Definition 2: divides half
- How it will help

```


```

visualise.

I am bad with numbers. Numbers make sense only when they measure something. I can't see 6 unless there's a picture, for example 6 apples, or 6 inches. They make even more sense when they show relativity, for example 6 apples is more than 2 apples, 6 inches is smaller than 10 inches. 


Median - 
	You have a group of people of different heights. For simplicity, assume there are odd number of people and every person has a unique height. Then you ask the question - "Who's the person who has half of the group with lesser height and the other half with more height?". A more clear question is "If we arrange them in a line in an order of increasing height, then who would lie in the exact middle?" The height of the person standing in the middle is called the median height of the group.

So median gives us a sense of the value that divides the set into two sets - lower and higher set. It would be more proper to say that the median value divides the whole set into lower-or-equal and higher-or-equal sets. In the above example we have assumed odd count of people with unique heights. There are minor modifications when there are even people and duplicate heights, but the overall idea is the same. For duplicate heights, the idea of arranging them in a line and picking the middle for median is valid. For even count, there won't be one middle person, there will be two. We can't just pick one of them as picking one would create inbalance in the sizes of lower and higher, they won't be 50% anymore. So its made fair by defining median in such cases to be the average of these two middle. There might not be a person present with the median height, but using that value we can separate the set into aforementioned equal-sized lower-or-equal and higher-or-equal sets.


Finding median of a stream - 
	What if a person is being added to the group periodically, how would you find the median of an ever-changing group. You could of course try to arrange them in a line with increasing height every time a new person is added and then get the middle for median. But can we do better? 

	This is where the magic of priority queue comes. 


```


#### What is median of a set?

A median is an element in the set that lies in the middle when the set is sorted.

> e.g. {8,2,4,5,7} -> sort -> {2,4,5,7,8} -> middle_value/median = 5

If the set has an even number of elements, then after the set is sorted there will be two middle elements and median would be the average of the two middle elements.

> e.g {3,2,6,6} -> sort -> {2,3,6,6} -> median = (3+6)/2 = 4.5

I find the above definition too mechanic. Says nothing about the significance of the median, just describes one way to find it for a given set.

What does a median say about the data.

Let's define two subsets. Lower(L) and higher(H) subsets.
each element in L <= each element in H

Median is the value using which we can make L and H of equal size if there are even count of elements. For odd count of elements, H could have one more  


