## 54. 3Sum Closest
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/3sum-closest/
### Two-Pointer Solution
Fix one element and apply the two-pointer technique to find the closest sum for the remaining two elements.

Approach:
1. Sort the input array `nums` to enable efficient two-pointer traversal.
2. Iterate through the array, fixing one element at a time.
3. For each fixed element:
   - Initialize two pointers: `left` (starting just after the fixed element) and `right` (at the end of the array).
   - Use the two-pointer approach to find the pair whose sum, along with the fixed element, is closest to the target.
   - Update the result whenever a smaller absolute difference from the target is found.
#### Java Implementation
```java
/**
 * Author: Craig Brown
 * Date:   May 6, 2025
 */ 
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        // Sort `nums`
        Arrays.sort(nums);
        int sum=nums[0]+nums[1]+nums[2];
        // Iterate through `nums`
        for(int i=0; i<nums.length; i++){
            int left=i+1;
            int right=nums.length-1;

            // Find the closest sum for each num[i]
            while(left<right){
                int cSum = nums[i]+nums[left]+nums[right];
                // Update the closest sum if this combination is closer to the target
                if(Math.abs(target-sum)>Math.abs(target-cSum) ){
                    sum=cSum;
                }
                // Utilize the sorted order to adjust pointers and minimize the difference to the target sum
                if(cSum<target){
                    left++;
                }else if(cSum>target){
                    right--;
                }else{
                    // If an exact match is found, return immediately
                    return target;
                }
            }
        }
        return sum;
    }
}
```
#### Python Implementation
```python
"""
Author: Craig Brown
Date:   May 8, 2025
"""
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        # Sort `nums`
        nums.sort()
        sum=nums[0]+nums[1]+nums[2]
        # Iterate through `nums`
        for i in range(len(nums)):
            left, right=i+1, len(nums)-1
            # Find the closest sum for each num[i]
            while left<right:
                cSum=nums[i]+nums[left]+nums[right]
                # Update the closest sum if this combination is closer to the target
                if abs(sum-target)>abs(cSum-target):
                    sum=cSum
                # Utilize the sorted order to adjust pointers and minimize the difference to the target sum
                if cSum<target:
                    left+=1
                elif cSum>target:
                    right-=1
                else:
                    # If an exact match is found, return immediately
                    return cSum
        return sum
```
#### Complexity Analysis
* Time Complexity: $O(n^2)$
  * Sort `nums`
    * Java implementation
      
      `Arrays.sort()` uses Dual-Pivot Quicksort for primitive types, with an average time complexity of $O(n \log n)$ and a worst-case complexity of $O(n^2)$.
    * Python implementation
      
      `list.sort()` has an average time complexity of $O(n \log n)$ and $O(n)$ in the best case (when the list is already sorted).

  * Iterate through `nums`
    
    The outer loop runs `n` times, and for each iteration, the inner loop iterates through the remaining elements (i.e., `n-i` times).
    The total number of iterations is the sum of `n-i` across all iterations: 
    $$\sum_{i=0}^{n-i} i = O(n^2)$$

  Thus, the overall time complexity is $O(n^2)$.

* Space Complexity: $O(\log n)$ in Java, $O(n)$ in Python
    * Java implementation
      
      `Arrays.sort()` uses Dual-Pivot Quicksort for primitive types, so the extra space is the recursion stack $O(\log n)$.
    * Python implementation
      
      `list.sort()` uses Timsort and requires linear space $O(n)$ for temporary storage.
#### Consideration
* All relevant combinations should be carefully considered by iterating through `nums`.