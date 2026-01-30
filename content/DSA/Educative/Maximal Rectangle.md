---
draft: false
tags:
  - dsa
  - dp
description: .
date: 2026-01-12
---

**Problem link** - https://leetcode.com/problems/maximal-rectangle/

```
Given a `rows x cols` binary `matrix` filled with `0`'s and `1`'s.
Find the largest rectangle containing only `1`'s and return its area.
```

#### Brute-force approach
This can be done by choosing a range of rows (`start_row, end_row`) and then a range of columns(`start_col, end_col`) and checking if all the cells have '1'. 

The time complexity would be O($n^2m^2$). 

#### Visualising Histogram
Let's change our approach a bit. We will now fix a row and consider all possible rectangles that have this row as base. If for a particular column there's a '0' for any row, then all the cells that lie above, which are present above the row having '0', won't contribute to rectangle forming. So from the fixed row, for each column we move upwards till we find the first '0'. It appears as a histogram, a bunch of vertical bars, over our fixed row. 

The greatest advantage of thinking in terms of histogram is that when we move the fixed row to the next row, we can recalculate the histogram for the new fixed row from the previous fixed row. How? If for column of the new fixed row, the value is '1' then the previous height of the bar in that cell is extended by 1, and if the value is '0' then the height of bar is reset to 0.

Using just the histogram approach, we can iterate over the row that'll act as base. And then keep just the values of the heights of the bar from that base row and update as mentioned above when we move to the next row. When we have the histogram for a particular row, we can choose a range of columns `start_col` and `end_col` and take the minimum of the the bar heights present within them as height of the rectangle.  
The time complexity of this approach would be O($n*(m+m^2)$)  $\approx$ O($n*m^2$) 

#### Maximum area of rectangle in a histogram
There exists a standard problem of finding maximum area of a rectangle in a given histogram that uses `stack` data structure. I won't be going into too much details for now. I may write about it when I get to solving problems belonging to "Stack" chapter. Let me try to briefly explain the idea behind it. While considering bars from left to right, if there's a higher bar at column `x` and then a smaller bar to its right at column `y`, then rectangles with the right vertical side aligned on columns to right of `y` can never be as high as the bar present at `x`. So as we calculate the for the bar at `x` before we proceed to `y`, and never consider the bar at `x` again in the future.

#### Tracking left and right boundaries of each bar
We know how high the bars are in the histogram as we fix the base row. What if for a bar over a particular column we could tell how far to left and right it could extend, meaning we can find bars of at least current bar.

![[Pasted image 20260113221258.png]]
_(Picture) bar at j can extend from j-2 to j+1 and form a rectangle_


To find the boundaries of a bar, first imagine the bar as individual cells stacked over each other.

![[Pasted image 20260113223038.png]]
_(Picture) bar is seen as cells stacked over each other_

![[Pasted image 20260113223306.png]]
_(Picture) rectangle with bar as height_

To get how far the bar can be stretched to left side, we can check by scanning backwards until we get a smaller bar. Same thing can be done for the right side by scanning forwards. This is fine, but since we should be accounting for each bar as possible heights, then scanning for each bar would take O($m^2$) for a row and if in total O($n*m^2$) for each possible base rows.


We are trying to find for a better time complexity.

What if each cell had the knowledge of how far it could stretch. 

![[Pasted image 20260113225026.png]]
_(Picture) cell at (i, j) knows that it could stretch from j-2 to j+2_

If we ask all the cells of a particular bar how far they could be stretch, then the minimum of all the stretches told by the cells is the stretch the bar could become height of a rectangle.

![[bar-2.gif]]
_(Picture) calculating stretch of bar from stretch of cells_

So we'll maintain at each cell how far there is a streak of '1's on left and right. To keep the left boundary we'll iterate from left to right and save the last column where there was a '0' then '1'. For right boundary, we'll do the same by iterating from right to left. We'll use this to calculate the left and right boundary of the bars at each column. The left boundary of the bar would be the maximum of all left boundaries of the cells of the bar, the right boundary would be the minimum of all boundaries. 

_Question: What would be the boundaries of a cell containing '0'?_
	Keep it $-inf$ for left boundary and $+inf$ for right boundary. This is because the bar over the '0' will be having zero height so the area formed will automatically be zero. And also when we move to the next row, the left boundary of the cell of this row will become the left boundary of the bar as `Math.max(-inf, x) = x`

#### Code and Complexity

**Time complexity:**   $O(n*m)$

**Space complexity**:  $O(m)$ for storing `histogram`, `leftBoundariesOfBars`, and `rightBoundariesOfBars`. 

> [!info]- Code (Maximal Rectangle)
> ```
> class Solution {
>     public int maximalRectangle(char[][] matrix) {
>         int n = matrix.length, m = matrix[0].length;
>         int[] histogram = new int[m];
>         int[] leftBoundariesOfBars = new int[m];
>         int[] rightBoundariesOfBars = new int[m];
> 
>         final int NEG_INF = -1, POS_INF = m+1;
>         Arrays.fill(leftBoundariesOfBars, NEG_INF);
>         Arrays.fill(rightBoundariesOfBars, POS_INF);
> 
>         int maxArea = 0;
> 
>         for (int i = 0; i < n; i++) {
> 
>             // Calculate histogram bars for each columns using previous row data
>             for (int j = 0; j < m; j++) {
>                 histogram[j] = matrix[i][j] == '0' ? 0: (histogram[j] + 1);
>             }
> 
>             // Calculate left boundaries of the cells for the row.
>             // Then use it to calculate left boundaries of the bars.
>             int lastLeftBoundaryThisRow = NEG_INF;
>             for (int j = 0; j < m; j++) {
>                 if (matrix[i][j] == '1') {
>                     if (j == 0 || matrix[i][j-1] == '0') {
>                         lastLeftBoundaryThisRow = j;
>                     }
> 
>                     leftBoundariesOfBars[j] = Math.max(leftBoundariesOfBars[j], lastLeftBoundaryThisRow);
> 
>                 } else {
>                     leftBoundariesOfBars[j] = NEG_INF;
>                 }
>             }
> 
>             // Calculate right boundaries of the cells for the row.
>             // Then use it to calculate right boundaries of the bars.
>             int lastRightBoundary = POS_INF;
>             for (int j = m-1; j >= 0; j--) {
>                 if (matrix[i][j] == '1') {
>                     if (j == m-1 || matrix[i][j+1] == '0') {
>                         lastRightBoundary = j;
>                     }
> 
>                     rightBoundariesOfBars[j] = Math.min(rightBoundariesOfBars[j], lastRightBoundary);
>                 
>                 } else {
>                     rightBoundariesOfBars[j] = POS_INF;
>                 }
>             }
> 
>             // For each bar as height, calulate area of rectangle.
>             for (int j = 0; j < m; j++) {
>                 int area = histogram[j] * (rightBoundariesOfBars[j] - leftBoundariesOfBars[j] + 1);
>                 maxArea = Math.max(maxArea, area);
>             }
> 
>             // System.out.println(Arrays.toString(histogram));
>             // System.out.println(Arrays.toString(leftBoundariesOfBars));
>             // System.out.println(Arrays.toString(rightBoundariesOfBars));
>             // System.out.println("______");
>         }
> 
>         return maxArea;
>     }
> }
> ```