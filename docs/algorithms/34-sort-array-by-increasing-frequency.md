## 34. Sort Array by Increasing Frequency
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/sort-array-by-increasing-frequency/

### Array Solution
Map elements in `nums` to indices of `freq`, where indices represent elements of `nums` and the values represent their frequency.
Since `-100 <= nums[i] <= 100`, shift indices by adding 100 to `num[i]`.

Example:
```text
nums:    [1,1,2,2,2,3]
indices:  0 1 2 3 4 5

freq:     0   2   3   1   0   0   ...  
indices:  100 101 102 103 104 105 ...
```

Filter out elements with `frequency>0` into `arr` and sort them by frequency.
Repeat each element in `arr` based on its frequency to form the final result.
#### Java Implementation
```java
class Solution {
    public int[] frequencySort(int[] nums) {
        // Map the elements in the `nums` array to the indices of the frequency array `freq`.
        // Since `-100 <= nums[i] <= 100`, add 100 to `num[i]` as a meaningful index.
        int[] freq = new int[201];
        for (int num : nums) {
            freq[num + 100]++;
        }

        // Filter out elements with a frenquency greater than 0 in descending order.
        int[] arr = new int[nums.length];
        int len = 0;
        for (int i = freq.length - 1; i >= 0; i--) {
            if (freq[i] > 0) {
                arr[len++] = i;
            }
        }

        // Sort the `arr` array with insertion sort.
        //             low
        //           i cmp
        //        i cmp 
        //     ...
        for (int low = 1; low < len; low++) {
            int inserted = arr[low];
            int i = low - 1;
            while (i >= 0 && freq[arr[i]] > freq[inserted]) {
                arr[i + 1] = arr[i];
                i--;
            }
            if (i != low - 1) {
                arr[i + 1] = inserted;
            }
        }

        // Repeat elements in the `arr` according to their frequency.
        int k = 0;
        for (int i = 0; i < len; i++) {
            int curFreq = freq[arr[i]];
            int num = arr[i] - 100;
            for (int j = 0; j < curFreq; j++) {
                nums[k++] = num;
            }
        }
        return nums;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n)$
    * Map the elements in the `nums` array to the indices of the frequency array `freq`.
        
        Traversing the `nums` array takes $O(n)$ time.
    * Filter out elements with a frequency greater than 0 in descending order.
    
        Since `freq` has a fixed size of `201` regardless of the input parameter, this step runs in $O(1)$ time.
    * Sort the `arr` array with insertion sort.

        Because $-100 \le nums[i] \le 100$, `len` is at most $201$.  
        The outer loop iterates `len-1` times, and the inner loop traverses elements from `i` down to `0`.  
        The total time complexity is:
        $$\sum_{i=0}^{len} i=\frac{len\times(len+1)}{2}=O(1)$$ 
        Thus, the time complexity of this step is $O(1)$.
    * Repeat elements in the `arr` according to their frequency.
        
        The number of elements to traverse in this step is equal to the size of `nums`, giving a time complexity of $O(n)$.

    Therefore, the overall time complexity is $O(n)$.
* Space Complexity: $O(n)$
    * The `freq` array has a fixed size, resulting in constant space complexity $O(1)$.
    * The `arr` array has the same size as `num`, leading to space complexity $O(n)$.
    Thus, the total space complexity is $O(n)$.
#### Consideration
* Each of `Arrays.stream(nums)`, `.boxed()` and `.collect(Collectors.toList())` has a time complexity of $O(n)$. 

    Manually copying values into a new list offers better performance compared to using Java Streams.