## 29. Power Set LCCI
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/power-set-lcci/
### Backtracking Solution
Use two recursive calls within a method to find subsets for result array `ans`.
* Both the first and the second calls exhibit the same behavior, saving the `path` array upon reaching the end of the `nums` array.
* After the first call completes, the current element is added to the shared `path` array.
* After the second call completes, the current element is removed frome the shared `path` array.

let's use `nums = [1,2,3,4]` as an example (using '#' to indicate when the index is exceeded):
```
indices: 0    1    2    3    4
nums:    1    2    3    4  
Recursion: 
         1 -> 2 -> 3 -> 4 -> #     (Save `[]`)
                        4 <- # (Add `4`)
                        4 -> #     (Save `[4]`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                   3 -> 4 -> #     (Save `[3]`)
                        4 <- # (Add `4`)  
                        4 -> #     (Save `[3,4]`)
              2 <- 3 <- 4 <- # (Remove `4` and `3`, then add `2`)
              2 -> 3 -> 4 -> #     (Save `[2]`)
                        4 <- # (Add `4`)
                        4 -> #     (Save `[2,4]`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                   3 -> 4 -> #     (Save `[2,3]`)
                        4 <- # (Add `4`)  
                        4 -> #     (Save `[2,3,4]`)
         1 <- 2 <- 3 <- 4 <- # (Remove `4`,`3` and `2`, then add `1`)
         1 -> 2 -> 3 -> 4 -> #     (Save `[1]`)
                        4 <- # (Add `4`)
                        4 -> #     (Save `[1,4]`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                   3 -> 4 -> #     (Save `[1,3]`)
                        4 <- # (Add `4`)  
                        4 -> #     (Save `[3,4]`)
              2 <- 3 <- 4 <- # (Remove `4` and `3`, then add `2`)
              2 -> 3 -> 4 -> #     (Save `[1,2]`)
                        4 <- # (Add `4`)
                        4 -> #     (Save `[1,2,4]`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                   3 -> 4 -> #     (Save `[1,2,3]`)
                        4 <- # (Add `4`)  
                        4 -> #     (Save `[1,2,3,4]`)
         1 <- 2 <- 3 <- 4 <- # (Remove `4`,`3`, `2` and `1`)
```
The final `ans` for the array `nums=[1,2,3,4]` is:
```
[
    [],[4],
    [3],[3,4],
    [2],[2,4],[2,3],[2,3,4],
    [1],[1,4],[1,3],[1,3,4],[1,2],[1,2,4],[1,2,3],[1,2,3,4]
]
```

let's focus on these backtracking processes:
```
indices: 0    1    2    3    4
nums:    1    2    3    4  
Recursion:
         1 -> 2 -> 3 -> 4 -> #    
                        4 <- # (Add `4`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                        4 <- # (Add `4`)  
              2 <- 3 <- 4 <- # (Remove `4` and `3`, then add `2`)
                        4 <- # (Add `4`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                        4 <- # (Add `4`)  
         1 <- 2 <- 3 <- 4 <- # (Remove `4`,`3` and `2`, then add `1`)
                        4 <- # (Add `4`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                        4 <- # (Add `4`)  
              2 <- 3 <- 4 <- # (Remove `4` and `3`, then add `2`)
                        4 <- # (Add `4`)
                   3 <- 4 <- # (Remove `4`, then Add `3`)
                        4 <- # (Add `4`)  
         1 <- 2 <- 3 <- 4 <- # (Remove `4`,`3`, `2` and `1`)
```
After the first recursive call completes and adds the current element to the `path` array, 
the second call follows the same process to generate all subsets.   
Finally, it removes the current element and backtracks to the previous index.

#### Java Implementation
```java
class Solution {
    private final List<List<Integer>> ans = new ArrayList<>();
    private final List<Integer> path = new ArrayList<>();
    private int[] nums;

    public List<List<Integer>> subsets(int[] nums) {
        this.nums = nums;
        dfs(0);
        return ans;
    }
    
    private void dfs(int i) {
        // End condition
        if (i == nums.length) { 
            ans.add(new ArrayList<>(path)); 
            return;
        }
        // Start the recursion at index `i`
        dfs(i + 1);
        path.add(nums[i]);
        // Restart the recursion at index `i`
        dfs(i + 1);
        path.remove(path.size() - 1); 
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n\times2^n)$

    let's use `nums = [1,2,3,4]` as an example:  
    At index `3`, only two calls are made, which return immediately as they match the end condition, then backtrack to the previous index `2`.  
    At index `2`, four calles are made since the recursion at index `3` repeated.  
    ...

    The number of calls doubles with each level of recursion, resulting in a time complexity of $O(2^n)$. 
    
    Since each recursive call copies the `path` array to `ans` using the constructor `public ArrayList(Collection<? extends E> c)`, which has a time complexity of $O(n)$, the overall time complexity is $O(n\times2^n)$.

* Space Complexity: $O(n\times2^n)$
    * Recursive Stack
    
        The recursion depth is at most `n` where `n` is the length of `nums`, leading to a stack space of $O(n)$.
    * `ans` array
  
        For a subset of length `m`, the array `nums` has $C(n,m)$ subsets of this size, where `n` is the length of `nums`.  
        Thus, the size of the `ans` array, which stores all subsets of `nums`, will be:
        $$1 \times C(n,1) + 2 \times C(n,2) + ... + n \times C(n,n)$$

        Use the Binomial Theorem to derive the result of the equation above: 
        $$(a + b)^n = \sum_{k=0}^n C(n, k) \times a^{n-k} \times b^k$$
        Setting `a=1` gives:
        $$(1 + b)^n = \sum_{k=0}^n C(n, k) \times b^k$$
        Differentiating both sides with respect to `x` and evaluating at `x=1` yields:
        $$\frac{d}{db}(1+b)^n = \frac{d}{db} \sum_{k=0}^n C(n, k) \times b^k$$
        Using the derivative rule $\frac{d}{dx}(x^n)=nx^{n-1}$, we get:
        $$n(1+b)^{n-1} = \sum_{k=0}^n C(n, k) \times kb^{k-1}$$
        Setting $b=1$ in both sides:
        $$n \times 2^{n-1} = \sum_{k=1}^n k \times C(n,k) = 1 \times C(n,1) + 2 \times C(n,2) + ... + n \times C(n,n)$$

        Thus, the final space complexity of `ans` is $O(n\times2^n)$

    * `path` array stores the current subset, which can contain up to `n` elements at any time, using $O(n)$ space.

   Therefore, the overall space complexity is $O(n\times2^n)$.

### Depth-first Search Solution
Save the `path` array at the start of each DFS call, 
recursively explore remaining indices, and remove the current element when backtracking.

Example: 
```
indices: 0    1    2    3    4
nums:    1    2    3    4  
Recursion:
         1 -> 2 -> 3 -> 4    [],[1],[1,2],[1,2,3],[1,2,3,4]
         1 -> 2      -> 4    [1,2,4]
         1      -> 3 -> 4    [1,3],[1,3,4]
         1           -> 4    [1,4]
              2 -> 3 -> 4    [2,3],[2,3,4]
              2      -> 4    [2,4]
                   3 -> 4    [3],[3,4]
                        4    [4]

```
The right arrays represent the subset added in the current recursive call.
#### Java Implementation
```java
class Solution {
    private final List<List<Integer>> ans = new ArrayList<>();
    private final List<Integer> path = new ArrayList<>();
    private int[] nums;

    public List<List<Integer>> subsets(int[] nums) {
        this.nums = nums;
        dfs(0);
        return ans;
    }

    private void dfs(int i) {
        ans.add(new ArrayList<>(path)); 
        for (int j = i; j < nums.length; j++) { 
            path.add(nums[j]);
            dfs(j + 1);
            path.remove(path.size() - 1); 
        }
    }
}

```
#### Complexity Analysis
* Time Complexity: $O(n\times2^n)$

    Each recursive call either includes or skips remaining elements, producing $\Theta(2^n)$ subsets (one copy of `path` per call).  

    The constructor `public ArrayList(Collection<? extends E> c)` copies the current subset in $O(n)$ time, so the overall time complexity is $O(n\times2^n)$. 

* Space Complexity: $O(n\times2^n)$
    * Recursive Stack
    
        The recursion depth is at most `n` where `n` is the length of `nums`, leading to a stack space of $O(n)$.
    * `ans` array has a space complexity of $O(n\times2^n)$, as explained in the previous Backtracking Solution.

    * `path` array stores the current subset, which can contain up to `n` elements at any time, using $O(n)$ space.

   Therefore, the overall space complexity is $O(n\times2^n)$.

### Traversal Solution
Let `n` be the length of the `nums` array.
Since each element might be included or excluded, the total number of subsets of `nums` array is $2^n$.

In the **binary representation** of subset indices from $0$ to $2^n$, each bit indicates whether an element is selected.

By iterating through all elements of `nums` and adding them to subsets at valid indices, 
every element is placed into the subsets that include it.

Example: 
```
nums:    1    2    3    4  

Subset indices: 0, 1, 2, 3, 4, 5, ..., 15.
Binary Representation: 
    0000, 
    0001, (`4` is selected)
    0010, (`3` is selected) 
    0011, (`3` and `4` are selected) 
    0100, (`2` is selected) 
    0101, (`2` and `4` are selected) 
    ..., 
    1111. (`1`,`2`,`3` and `4` are selected) 
```
#### Java Implementation
```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        int n = nums.length;
        // A list containing all subsets.
        List<List<Integer>> ans = new ArrayList<>(1 << n);
        // Traverse all subset indices, where `i` represents all combinations in binary.
        for (int i = 0; i < (1 << n); i++) { 
            // Add the current subset to the list.
            List<Integer> subset = new ArrayList<>();
            // Traverse the indices of `nums`
            for (int j = 0; j < n; j++) {
                // Extracts the bit at index `j` from `i`'s binary representation to check if it is `1`.
                if ((i >> j & 1) == 1) { 
                    subset.add(nums[j]);
                }
            }
            ans.add(subset);
        }
        return ans;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n\times2^n)$

    The outer loop iterates over all subset indices, running in $O(2^n)$ time.
    The inner loop traverses the bit indices of the binary representation of $2^n$, with a time complexity of $O(n)$.

    Therefore, the overall time complexity is $O(n\times2^n)$.
* Space Complexity: $O(n\times2^n)$

    `ans` array has a space complexity of $O(n\times2^n)$, as explained in the previous Backtracking Solution.

    Thus, the overall space complexity is $O(n\times2^n)$.