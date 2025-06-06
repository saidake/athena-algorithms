## 11. Minimum Deletions to Make String Balanced
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/minimum-deletions-to-make-string-balanced/

### Dynamic Programming Solution
Define `f[i]` to represent the minimum deletions required for the first `i` characters of the string `s`. As we traverse the string `s`, the logic is as follows:

- If the current character is `'b'`, there is no need to delete it.
- If the current character is `'a'`:
  - Deleting it results in `f[i] = f[i-1] + 1`, as the current character is removed.
  - Keeping it requires deleting all `'b'` before the current `'a'`.

Introduce a variable `countB` to represent the number of `'b'` characters encountered in the first `i` characters of the string `s`. 
The dynamic programming formula will be:
```text
f[i] = 
    f[i-1],                   if the current character is 'b'
    min(f[i-1] + 1, countB),  if the current character is 'a'
```
#### Java Implementation
```java
class Solution {
    public int minimumDeletions(String s) {
        int f = 0, countB = 0;
        for (char c: s.toCharArray())
            if (c == 'b'){
                ++countB; 
            } else {
                f = Math.min(f + 1, countB);
            }
        return f;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n)$

    The `for` loop takes $O(n)$ time.
* Space Complexity: $O(n)$

    `s.toCharArray()` allocates a character array of length $n$, so the extra space is $O(n)$.