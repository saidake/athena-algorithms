## 39. Trapping Rain Water II
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/trapping-rain-water-ii/

### Heap Solution 
According to the above 2D elevation map, only units surrounded by higher units can trap rain.

To calculate the total volume of trapped water:
* Traverse units from the boundary. The trapped water area is determined by finding the minimum height in the boundary and gradually reducing its range.

Define a boundary queue `boundary` to store the boundary units and an array `visited` to mark visited units.
Use `totalVolume` for the total trapped water volume and `lhu` for the current lowest-height unit.

Use `[]` to indicate visited units.  
Example:
```
1  4  3  1  3  2
3  2  1  3  2  4
2  3  3  2  3  1
3  2  1  3  2  3

Step 1 (Initialize the boundary queue and mark all boundary units):
    [1][4][3][1][3][2]
    [3] 2  1  3  2 [4]
    [2] 3  3  2  3 [1]
    [3][2][1][3][2][3]

    lhu=1
    totalVolume=0
Step 2 (Reduce the boundary):
    [1][4][3][1][3][2]
    [3] 2  1 [3] 2 [4]
    [2] 3  3  2  3 [1]
    [3][2][1][3][2][3]

    lhu=1 (Poped unit `1` from the boundary queue, added `3` to `boundary`, and marked 1 and 3 as visited.)
    totalVolume=0 
Step 3:
    [1][4][3][1][3][2]
    [3] 2  1 [3] 2 [4]
    [2] 3 [3] 2  3 [1]
    [3][2][1][3][2][3]

    lhu=1
    totalVolume=0 
Step 4:
    [1][4][3][1][3][2]
    [3] 2  1 [3] 2 [4]
    [2] 3 [3] 2 [3][1]
    [3][2][1][3][2][3]

    lhu=2
    totalVolume=0 
Step 5:
    [1][4][3][1][3][2]
    [3] 2  1 [3] 2 [4]
    [2][3][3] 2 [3][1]
    [3][2][1][3][2][3]

    lhu=3
    totalVolume=0 
Step 6:
    [1][4][3][1][3][2]
    [3] 2 [1][3] 2 [4]
    [2][3][3] 2 [3][1]
    [3][2][1][3][2][3]

    lhu=3
    totalVolume=2
Step 7:
    [1][4][3][1][3][2]
    [3] 2 [1][3] 2 [4]
    [2][3][3][2][3][1]
    [3][2][1][3][2][3]

    lhu=3
    totalVolume=2+1=3

Step 8:
    [1][4][3][1][3][2]
    [3][2][1][3] 2 [4]
    [2][3][3][2][3][1]
    [3][2][1][3][2][3]

    lhu=3
    totalVolume=3+1=4


Step 9:
    [1][4][3][1][3][2]
    [3][2][1][3][2][4]
    [2][3][3][2][3][1]
    [3][2][1][3][2][3]

    lhu=3
    totalVolume=4+1=5

Total volume of trapped water: 5
```

#### Java Implementation
```java
class Solution {
    
    public class Cell{
    int row;
    int col;
    int height;
        public Cell(int row, int col, int height){
            this.row = row;
            this.col = col;
            this.height = height;
        }
    }

    Comparator<Cell> comp = new Comparator<>(){
        public int compare(Cell left, Cell right){
            return left.height - right.height;
        }
    };
    
    public int trapRainWater(int[][] heightMap) {
        
        if (heightMap == null || heightMap.length == 0 || heightMap[0].length == 0) return 0;
        
        int m = heightMap.length;
        int n = heightMap[0].length;
        
        PriorityQueue<Cell> boundary = new PriorityQueue<Cell>(m*n, comp);
        boolean[][] visited = new boolean[m][n];
        // Initialize the `boundary` and `visited` array with the first and last columns.
        for(int i = 0; i < heightMap.length; i++){
            boundary.offer(new Cell(i, 0, heightMap[i][0]));
            visited[i][0] = true;
            boundary.offer(new Cell(i, n-1, heightMap[i][n-1]));
            visited[i][n-1] = true;
        } 
        // Initialize the `boundary` and `visited` array with the first and last rows.
        for(int i = 0; i < heightMap[0].length; i++){
            boundary.offer(new Cell(0, i, heightMap[0][i]));
            visited[0][i] = true;
            boundary.offer(new Cell(m-1, i, heightMap[m-1][i]));
            visited[m-1][i] = true;
        }
        
        int totalVolume = 0;
        int[][] dirs = new int[][]{{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        // Iterate over `boundary`
        while(!boundary.isEmpty()){
            // Find the lowest-height unit in the `boundary` queue.
            Cell lhu = boundary.poll(); 
            int row = lhu.row;
            int col = lhu.col;
            // Check the surrounding units
            for(int i = 0; i < 4; i++){
                int rowPos = dirs[i][0] + row;
                int colPos = dirs[i][1] + col;
                // Check if the position is valid and unvisited
                if(rowPos >= 0 && rowPos < m 
                   && colPos >= 0 && colPos < n 
                   && !visited[rowPos][colPos] ) {
                    // Decrease 
                    totalVolume += Math.max(0, lhu.height - heightMap[rowPos][colPos]);
                    visited[rowPos][colPos] = true;
                    // The height added to the queue is the one trapped the water.
                    boundary.offer(new Cell(rowPos, colPos, Math.max(heightMap[rowPos][colPos], cell.height)));
                }
            }
        }
        return totalVolume;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(mn \log (mn))$
  * Initialize the `boundary` and `visited` array with the first and last columns.
    
    Traversing the row indices of `heightMap` takes $O(m)$ time and the `offer` method of `PriorityQueue` takes $O(\log (mn))$, 
     resulting in a total time complexity of $O(m\log (mn))$, where `m` is the row length and `n` is the column length.

  * Initialize the `boundary` and `visited` array with the first and last rows.

    Similar to the previous step, this loop has a time complexity of $O(n \log (mn))$.

  * Iterate over `boundary`
    
    Since visited units are not traversed while reducing the boundary, the outer loop iterates over all units, taking $O(mn)$ time.  
    The inner loop has a fixed `4` iterations, and the `offer` method of `PriorityQueue` takes $O(\log (mn))$ time, 
     resulting in a total time complexity of $O(mn \log (mn))$.

  Therefore, the total time complexity is $O(mn \log (mn))$.

* Space Complexity: 
  * The priority queue `boundary` and array `visited` each take $O(m\times n)$ space.
  * `dirs` has a fixed size `4`, taking $O(1)$ space.
  
  Thus, the total space complexity is $O(m\times n)$.