## 51. Quick Sort
**Back:** [README](../../README.md)  
### Description
Given an integer array `arr`, sort it in ascending order.

#### Constraints:
* `1 <= arr.length <= 10^4`
* `-10^4 <= arr[i] <= 10^4`

### Depth-first Search Solution
1. Partition the array using Lomuto’s partition scheme so that elements to the left of the `pivot` are less than it, and elements to the right are greater.
2. Recursively partition the left and right subarrays until each contains at most two elements.
   * Note that the `pivot` is excluded, as its final position is already determined.

```
arr:
    38, 27, 43, 3, 9, 82, 10

Step-by-step partitions:
            [38, 27, 43, 3, 9, 82, 10] 
           /             |            \ 
    [27, 3, 9]          10        [43, 82, 38] 
   /    |     \                   /    |     \ 
[3]     9    [27]              [38]    43     [82]
```
#### Java Implementation
```java
/**
 * @author Craig Brown
 * @date April 18, 2025
 **/
public class QuickSort {
    public static void quickSort(int[] arr, int left, int right) {
        if (left < right) {
            // Select the rightmost value as the pivot.
            int pivot=arr[right];
            int i=left-1, j=left;  // left -1 <= i < right, i < j < right
            // Maintain elements less than the pivot at or before index i (Lomuto Partition)
            for(;j<right;j++){
                if(arr[j]<pivot){
                    i++;
                    int temp=arr[j];
                    arr[j]=arr[i];
                    arr[i]=temp;
                }
            }
            // Place pivot after the last smaller element
            int temp=arr[i+1];
            arr[i+1]=arr[right];
            arr[right]=temp;
            // Recursively sort left and right partitions (excluding pivot)
            int pivotIndex = i + 1;  // left <= pivotIndex <= right

            quickSort(arr, left, pivotIndex-1);
            quickSort(arr, pivotIndex+1, right);
        }
    }

    public static void main(String[] args) {
        int[] arr = {38, 27, 43, 3, 9, 82, 10};
        quickSort(arr, 0, arr.length - 1);
        for (int num : arr) {
            System.out.print(num + " ");
        }
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n\log n)$(Averge Case), $O(n^2)$ (Worst Case) 
  * For a complete recursion binary tree, the total number of elements `n` and the depth `D` satisfy $n=2^D-1$.
    so the recursion depth is approximately $\log n$, where `n` is the size of `arr`.

    At every level `D`, all `n` elements in `arr` are traversed for partitioning, despite being split into subarrays. This results in a time complexity of $O(n)$ per level.
  * In the worst case (e.g., when the pivot is always the smallest or largest element), the recursion tree degenerates into a linked list, leading to a time complexity of $O(n^2)$.  
  
  Therefore, the average time complexity is $O(n\log n)$, and the worst-case is $O(n^2)$.  
  Since each partition step excludes the `pivot`, quick sort tends to outperform merge sort in practice.

* Space Complexity: $O(\log n)$ (Averge Case), $O(n)$ (Worst Case) 
  * The recursion depth is approximately $\log n$, requiring only constant space at each level.
  
  * In the worst case (e.g., when the pivot is always the smallest or largest element), the recursion depth becomes $n$, leading to $O(n)$ space complexity. 

  Thus, the average space complexity is $O(\log n)$, and the worst-case is $O(n)$.
#### Consideration
* The `if (left < right)` check ensures no out-of-bounds access, even if the indices exceed valid ranges.