## 28. Merge Sorted Array
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/merge-sorted-array/

### Two-Pointer Solution
Copy a new array `nums1Cp` from `nums1` as a comparing array, and merge elements from arrays 'nums1Cp' and 'nums2' into 'nums1':
Copy the array `nums1` into a new array `nums1Cp` for comparison, and merge elements from `nums1Cp` and `nums2` into `nums1` as follows:

* If both array `nums1Cp` and `nums2` have remaining elements and `nums1Cp[i] < nums2[k]`, insert the smaller value from `numsCp` into `nums1`.
* If `nums2` has no remaining elements, insert the current element from `nums1Cp` directly into `nums1`;
* If `nums1Cp` is empty or `nums2` contains the smaller value, insert the value from `nums2` into `nums1`.
### Implementation
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        int[] nums1Cp=Arrays.copyOf(nums1, m);
        // Merge elements from arrays 'nums1Cp' and 'nums2' into 'nums1'.
        for(int i=0, j=0, k=0; i<nums1.length; i++){ 
            if( j<m && k<n && nums1Cp[j]<nums2[k] || j<m  && k>=n  ){
                nums1[i]=nums1Cp[j];
                j++;
            }else {
                nums1[i]=nums2[k];
                k++;
            }
        }
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(m + n)$

    The `Arrays.copyOf` method takes $O(m)$ time and the `for` loop iterates `nums1.length = m + n` times, 
    resulting in an overall time complexity of $O(m + n)$.

* Space Complexity: $O(m)$

    The array `nums1Cp` occupies $O(m)$ space where `m` is the first `m` integers in array `nums1`.