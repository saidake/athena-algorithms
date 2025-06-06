## 47. Beautiful Towers II
**Back:** [README](../../README.md)  

### Source
https://leetcode.cn/problems/beautiful-towers-ii/

### Monotonic Stack Solution
Define a monotonically increasing stack `stack` to compute the maximum sum of an increasing subarray `incList1` from the beginning of `maxHeights` or `incList2` from the end.
* Since the previous larger values must be excluded and the relative order must be preserved, a monotonic stack is well-suited for this task.
* The goal is to find the maximum possible sum of heights in a valid configuration, which is achieved by combining prefix and suffix contributions.
$$res=max(sum(incList1[i])+sum(incList2[i]))$$  
    
  * `incList1[i]` represents the sum of the first `i` elements in the increasing sequence from the start. 
  The current element should be excluded from `incList1` to avoid double-counting it in both sums.  
  For `i=0`, the `incList1[0]=0`.  
  * The `incList2[i]` represent the sum of first `i+1` elements of `incList2`.

Approach: 
1. Traverse `maxHeights` from left to right to compute `incList1`, storing the maximum prefix sum at each index.
2. Traverse `maxHeights` from right to left to compute `incList2`, storing the maximum suffix sum.
    * At each index, calculate the total height sum as `incList1[i] + incList[2]`, and update `res` with the maximum value found.
#### Java Implementation
```java
/**
 * @author Craig Brown
 * @date April 4, 2025
 */
class Solution {
    public long maximumSumOfHeights(List<Integer> maxHeights) {
        // Convert `maxHeights` to `arr`
        int[] arr = new int[maxHeights.size()];  
        for(int i=0; i<maxHeights.size(); i++){
            arr[i]=maxHeights.get(i);
        }
        // Common data
        ArrayDeque<Integer> stack=new ArrayDeque<>();
        int len = arr.length;
        long[] incList1=new long[len+1];
        incList1[0]=0;
        stack.push(-1);
        long sum1=0;
        // Traverse `arr` from the beginning
        for(int i=0; i<len; i++){
            int cur=arr[i];
            // Deduct earlier larger values from `sum1`
            while(stack.size()>1 && cur <= arr[stack.peek()]){
                int preInd=stack.pop();
                sum1-=(long) arr[preInd]*(preInd-stack.peek());
            }
            // Push the current index to the `stack`
            sum1+=(long) cur*(i-stack.peek());
            stack.push(i);
            incList1[i+1]=sum1;
        }

        stack.clear();
        stack.push(len);
        // There's no need to define `incList2`, simply use `sum2` directly at each index i.
        long sum2=0; 
        long res=sum1;
        // Traverse `arr` from the end
        for(int i=len-1; i>=0; i--){
            int cur=arr[i];
            while(stack.size()>1 && cur<=arr[stack.peek()]){
                int preInd=stack.pop();
                sum2-=(long) arr[preInd]*(stack.peek()-preInd);
            }
            sum2+=(long) cur*(stack.peek()-i);
            stack.push(i);
            res = Math.max(res,incList1[i]+sum2);
        }
        return res;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n)$
  * Convert `maxHeights` to `arr`
    
    This loop runs in $O(n)$ time, where `n` is the length of `maxHeights`, as it iterates through each element once.
  * Traverse `arr` from the beginning
    
    The operations `stack.push`, `stack.pop` and `stack.peek` all have constant time complexity.
    The outer loop runs in $O(n)$ time to traverse `arr`,
    The inner loop deducts up to `n` values.
    
    The total time complexity of this step is $O(2n)$，which simplifies to $O(n)$.
  * Traverse `arr` from the end
  
    Similiar to the previous step, this step also has a time complexity of $O(n)$.

  Therefore, the overall time complexity is $O(n)$.
* Space Complexity: $O(n)$
  * `arr` and `sum1List` each use $O(n)$ space, where `n` is the length of `maxHeights`.
  * `stack` takes $O(n)$ space in the worst case when all values increase from either the beginning or the end.

  Thus, the overall space complexity is $O(n)$
#### Consideration
* Use a primitive array instead of `java.util.List` to avoid unnecessary type boxing and array resizing.
* `java.util.ArrayDeque` offers better performance than `java.util.Stack` as `Stack` is synchronized and incurs additional locking overhead.
* When the result may exceed the limits of the data type, relying on automatic type conversion can lead to precision loss.  
  In such cases, explicit type casting should be used to prevent overflow:  `sum1 -= (long) arr[preInd] * (preInd - stack.peek());`