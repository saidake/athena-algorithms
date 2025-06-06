## 24. Find the Number of Ways to Place People I
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/find-the-number-of-ways-to-place-people-i/

### Analysis
Sort the `points` array in ascending order by the `x` coordinate, and in descending order by the `y` coordinate when `x` coordinates are the same.

Traverse the points from left to right. 
For each point `points[i]`, attempt to find a point at the lower right (`points[j][1] <= points[i][1]`) through a child traversal:
- If `points[j][1]` is greater than any previously traversed point in the child traversal, no other points exist within the rectangle.
- If `points[j][1]` is smaller than a previously traversed point, other points exist in the rectangle, violating the requirements.

### Implementation
``` java
class Solution {
    public int numberOfPairs(int[][] points) {
        Arrays.sort(points, (p, q) -> p[0] != q[0] ? p[0] - q[0] : q[1] - p[1]);
        int ans = 0;
        for (int i = 0; i < points.length; i++) {
            int y0 = points[i][1];
            int maxY = Integer.MIN_VALUE;
            for (int j = i + 1; j < points.length; j++) {
                int y = points[j][1];
                if (y <= y0 && y > maxY) {
                    maxY = y;
                    ans++;
                }
            }
        }
        return ans;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n^2)$
    * `Arrays.sort` has a time complexity of $O(nlogn)$.

    * The main loop
        The loop iterate over all remaining elements in the worst case, with a total execution time of $\sum_{j=1}^n j = \frac{n^2+n}{2}$, resulting a time complexity of $O(n^2)$.
    
    Thus, the overall time time complexity is $O(n^2)$
* Space Complexity: $O(n)$
  
  `Arrays.sort(points, comparator)` sorts an `int[][]`, which is an object array.  
  Java TimSort requires $O(n)$ workspace for object arrays, not the $O(\log n)$ stack used by Dual-Pivot Quicksort on primitive arrays.

    The total space complexity is $O(n)$.

Note that the space complexity of `Arrays.sort` is:
* $O(\log n)$ for sorting primitive arrays.
* $O(n)$ for sorting object arrays.