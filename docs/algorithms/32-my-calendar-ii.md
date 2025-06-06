## 32. My Calendar II
**Back:** [README](../../README.md)  
### Source
https://leetcode.com/problems/my-calendar-ii/

### Array Solution
Define an array `self.cal` to store inserted elements and `self.cnt` to track the overlap count at each `startTime` index.
 * The value in `self.cnt` at `startTime` represents the total overlap count at that moment.
 * The value in `self.cnt` at `endTime` indicates the number of events that cover the current event.

Approach: 
1. Insert `endTime`.
   * if it already exists, ignore it, as the number of events that cover the current event remain unchanged.
   * if it doesn't exist, directly copy `self.cnt[r-1]` to `self.cnt[r]`, as explained below:
      * If `self.cal[r-1]` is a `startTime`, the current event must overlap with the previous one.
        
        Copy `self.cnt[r-1]` to `self.cnt[r]` since there are now `self.cnt[r-1]` events now cover the current event.
      * If `self.cal[r-1]` is an `endTime`, the overlap situation with the previous one can't be determined but the current event must overlap with the events that cover the previous event.
    
        Similarly, copy `self.cnt[r-1]` to `self.cnt[r]` since `self.cnt[r-1]` events now cover the current event.

2. Increment the overlap count for the affected range.
3. Insert `startTime`.
    * If `startTime` already exists, increment its overlap count.
    * If `startTime` does not exist, directly copy `self.cnt[l-1]` to `self.cnt[l]` and increase it by `1`, explained as follows:

      * If `self.cal[l-1]` is a `startTime`, the current event must overlap with the previous one.

        Copy `self.cnt[l-1]` to `self.cnt[l]` and increment it by `1`, as `self.cnt[l-1] + 1` events now cover the current event.

      * If `self.cal[l-1]` is an `endTime`, the current event does not overlap with it but overlaps with the events that covers the previous event.

        Similarly, copy `self.cnt[l-1]` to `self.cnt[l]` and increment it by `1`, as `self.cnt[l-1] + 1` events cover the current event.

Example: 
```
Input:
["MyCalendarTwo","book","book","book","book","book"]
[[],[1,2],[1,3],[3,8],[2,3],[2,4]]

Step 1:
    Insert [1,2]
    Before: 
        self.cal: [0, inf]
        self.cnt: [0, 0]
        l: 1 r: 1
    After:
        self.cal: [0, 1, 2, inf]
        self.cnt: [0, 1, 0, 0]
Step 2:
    Insert [1,3]
    Before: 
        self.cal: [0, 1, 2, inf]
        self.cnt: [0, 1, 0, 0]
        l: 2 r: 3
    After:
        self.cal: [0, 1, 2, 3, inf]
        self.cnt: [0, 2, 1, 0, 0]
Step 3:
    Insert [3,8]
    Before: 
        self.cal: [0, 1, 2, 3, inf]
        self.cnt: [0, 2, 1, 0, 0]
        l: 4 r: 4
    After:
        self.cal: [0, 1, 2, 3, 8, inf]
        self.cnt: [0, 2, 1, 1, 0, 0]
Step 4:
    Insert [2,3]
    Before: 
        self.cal: [0, 1, 2, 3, 8, inf]
        self.cnt: [0, 2, 1, 1, 0, 0]
        l: 3 r: 3
    After:
        self.cal: [0, 1, 2, 3, 8, inf]
        self.cnt: [0, 2, 2, 1, 0, 0]
Step 5:
    Insert [2,4]
    Before: 
        self.cal: [0, 1, 2, 3, 8, inf]
        self.cnt: [0, 2, 2, 1, 0, 0]
        l: 3 r: 4
    Overlap check failed, return false.
```

Example usage of `bisect_right` and `bisect_left`:
```
a = [3, 8]
b = [3, 3, 8, 8]
c = [3, 3, 5, 8, 8]

L, R = bisect_right(a, val1), bisect_left(a, val2)

Case 1 (val2 <= 3):
[3, 8]
 L
 R
[3, 3, 8, 8]
 L
 R

Case 2 (val1 >= 3, val2 <=8):
[3, 8]
    L
    R
[3, 3, 8, 8]
       L
       R 
[3, 3, 5, 8, 8]
       L  R 

Case 3 (val1 >= 3 and val1 < 8, val2 >=8):
[3, 8]
    L
    R  
[3, 3, 8, 8]
       L
          R 
[3, 3, 5, 8, 8]
       L     R 


Case 4 (val1 >=8):
[3, 8]
    L
    R
[3, 3, 8, 8]
          L
          R 
```

#### Pyton3 Implementation
```python
class MyCalendarTwo:

    def __init__(self):
        self.cal, self.cnt = [0, inf], [0, 0]

    def book(self, startTime: int, endTime: int) -> bool:
        # Find insertion positions for startTime and endTime
        l, r = bisect_right(self.cal, startTime), bisect_left(self.cal, endTime)

        # print("------------------------------------------")
        # print(f"Insert [{startTime},{endTime}]") 
        # print(f"self.cal: {self.cal}") 
        # print(f"self.cnt: {self.cnt}") 
        # print(f"l: {l} r: {r}") 

        # Check if the new booking would cause a triple booking
        if 2 in self.cnt[l-1:r]: 
            return False

        # Insert `endTime` to `self.cal` and `self.cnt` if it not exists
        if endTime < self.cal[r]: 
            self.cal.insert(r, endTime)
            self.cnt.insert(r, self.cnt[r - 1])

        # Increment overlap count for the affected range
        for i in range(l, r): 
            self.cnt[i] += 1

        # Insert `startTime`
        if startTime == self.cal[l - 1]: 
            self.cnt[l - 1] += 1
        else:
            # If startTime doesn't exist, copy overlap count from index `l-1` and increase it.
            self.cal.insert(l, startTime)
            self.cnt.insert(l, self.cnt[l - 1] + 1)
        # print(f"self.cal: {self.cal}")
        # print(f"self.cnt: {self.cnt}")
        return True
```
#### Complexity Analysis
* Time Complexity: 
  * Find insertion positions for startTime and endTime
    
    Both `bisect_right` and `bisect_left` have a time complexity of $O(\log n)$.
  * Check if the new booking would cause a triple booking

    In the worst case, it traverses all elements in the `self.cnt` array, resulting in a time complexity of $O(n)$.
  * Insert `endTime` to `self.cal` and `self.cnt` if it not exists  

    If the condition is `true`, the `insert` method takes $O(n)$ time.
  * Increment overlap count for the affected range
  
    In the worst case, it traverses all elements in the `self.cnt` array, resulting in a time complexity of $O(n)$.

  * Insert `startTime`
  
    If `startTime` is not found, the `insert` method takes $O(n)$ time.
    
  For each booking, the time complexity is $O(n)$ in the worst case due to the insertion and potential shifting of elements in the list dominate the overall time.

  For `n` bookings, the total time complexity would be $O(n^2)$ in the worst case.

* Space Complexity: $O(n)$
  * Both `self.cal` and `sel.cnt` requires $O(n)$ space where `n` is the number of bookings.

  Thus, the space complexity is $O(n)$.

#### Java Implementation
```java
import java.util.ArrayList;

class MyCalendarTwo {
    private ArrayList<Integer> cal;  // List of event times
    private ArrayList<Integer> cnt;  // List of overlap counts

    public MyCalendarTwo() {
        cal = new ArrayList<>();
        cnt = new ArrayList<>();
        // Initialize with sentinel values
        cal.add(0);
        cal.add(Integer.MAX_VALUE);  // Using MAX_VALUE instead of Python's inf
        cnt.add(0);
        cnt.add(0);
    }

    public boolean book(int startTime, int endTime) {
        // Find insertion positions for startTime and endTime
        int l = bisectRight(cal, startTime);  // Position where startTime fits
        int r = bisectLeft(cal, endTime);     // Position where endTime fits

        // Check if the new booking would cause a triple booking
        for (int i = l - 1; i < r; i++) {
            if (cnt.get(i) >= 2) {
                return false;
            }
        }

        // Insert `endTime` to `self.cal` and `self.cnt` if it not exists  
        if (endTime < cal.get(r)) {
            cal.add(r, endTime);
            cnt.add(r, cnt.get(r - 1));
        }

        // Increment overlap count for the affected range
        for (int i = l; i < r; i++) {
            cnt.set(i, cnt.get(i) + 1);
        }

        // Insert `startTime`
        if (startTime == cal.get(l - 1)) {
            cnt.set(l - 1, cnt.get(l - 1) + 1);
        } else {
            cal.add(l, startTime);
            cnt.add(l, cnt.get(l - 1) + 1);
        }

        return true;
    }

    // Binary search to find the rightmost insertion point for target
    private int bisectRight(ArrayList<Integer> list, int target) {
        int left = 0;
        int right = list.size();
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (list.get(mid) <= target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }

    // Binary search to find the leftmost insertion point for target
    private int bisectLeft(ArrayList<Integer> list, int target) {
        int left = 0;
        int right = list.size();
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (list.get(mid) < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return left;
    }
}
```
#### Complexity Analysis
* Time Complexity: 
  * Find insertion positions for startTime and endTime
    
    Both `bisectRight` and `bisectLeft` have a time complexity of $O(\log n)$.
  * Check if the new booking would cause a triple booking

    In the worst case, it traverses all elements in the `cnt` array, resulting in a time complexity of $O(n)$.
  * Insert `endTime` to `cal` and `cnt` if it not exists  

    If the condition is `true`, the `insert` method takes $O(n)$ time.
  * Increment overlap count for the affected range
  
    In the worst case, it traverses all elements in the `cnt` array, resulting in a time complexity of $O(n)$.

  * Insert `startTime`
  
    If `startTime` is not found, the `insert` method takes $O(n)$ time.
    
  For each booking, the time complexity is $O(n)$ in the worst case due to the insertion and potential shifting of elements in the list dominate the overall time.

  For `n` bookings, the total time complexity would be $O(n^2)$ in the worst case.

* Space Complexity: $O(n)$
  * Both `cal` and `cnt` requires $O(n)$ space where `n` is the number of bookings.

  Thus, the space complexity is $O(n)$.

### Segment Tree Solution
Use a segment tree to efficiently store events. 

Define a fixed node `root` to represent the root event in the tree.
* Use `O` to denote the presence of a range to the left or right of the current node, 
`X` to denote the absence of such a range, 
* Use `start` for the start of a new range, and `end` for the end of the new range.

Approach:  

When a new booking event arrives, recursively check the left and right nodes from `root`.

  * If the new range does not overlap with the current node `node`, continue comparing it with `node.left` or `node.right` until a `null` location is found for insertion or further overlap occurs, requiring another split.  
    Example:   
    * If the new range is to the left of `node`, and `node.left` exits, continue comparing 
        ```
                        O [node.start, node.end] X   
        [start, end]  
        ```
    * If the new range is to the left of `node`, and `node.left` is `null`, insert `[start, end]` there.
        ```
                        X [node.start, node.end] O   
        [start, end]  
        ```
  * If the new range overlaps with `node`, split the two ranges into three parts, set the middle range as the new `node` and compare the left range with `node.left` and the right range with `node.right` until a `null` location is found for insertion or further overlap occurs, requiring another split.    

    Example: 
    ```
    [start, end]
       X [node.start, node.end] O
    ```
    Seperate the above two ranges into three ranges: `[start, node.start]`, `[node.start, end]`, and `[end, node.end]`.  
    If the `node.left` or `node.right` exits, compare `[start, node.start]` with `node.left` or `[end, node.end]` with `node.right` until a `null` location is found for insertion or another split is needed.


#### Pyton3 Implementation
```python
class SegmentTree:
    def __init__(self, startTime, endTime):
        self.left = None
        self.right = None
        self.overlap = False
        self.startTime = startTime
        self.endTime = endTime

class MyCalendarTwo:
    def __init__(self):
        self.root = None

    def book(self, startTime, endTime):
        # Check if the new event can be booked
        if not self.insertable(self.root, startTime, endTime):
            return False  
        # Insert the new event into the tree
        self.root = self.insert(self.root, startTime, endTime)
        return True

    def insertable(self, node, startTime, endTime):
        # If the range is invalid, it's always insertable
        if startTime >= endTime: 
            return True
        if node is None: 
            return True
        # Check if the new event is on the right side
        if startTime >= node.endTime:
            return self.insertable(node.right, startTime, endTime)
        # Check if the new event is on the left side
        elif endTime <= node.startTime:
            return self.insertable(node.left, startTime, endTime)
        # If there's an overlap, return False
        else:
            if node.overlap: 
                return False
            # Recursively check the left and right subtrees for insertability
            return (self.insertable(node.left, startTime, node.startTime) and 
                    self.insertable(node.right, node.endTime, endTime))

    def insert(self, node, startTime, endTime):
        # If the range is invalid, return the node as is
        if startTime >= endTime: 
            return node  
        # Create a new node if the current one is None
        if node is None: 
            return SegmentTree(startTime, endTime) 
        # If the new event is on the right side, insert it there
        if startTime >= node.endTime:
            node.right = self.insert(node.right, startTime, endTime)
        # If the new event is on the left side, insert it there
        elif endTime <= node.startTime:
            node.left = self.insert(node.left, startTime, endTime)
        # If there's an overlap, mark the current node and split the range
        else:
            node.overlap = True
            # Merge ranges if necessary
            a = min(startTime, node.startTime)
            b = max(startTime, node.startTime)
            c = min(endTime, node.endTime)
            d = max(endTime, node.endTime)
            node.left = self.insert(node.left, a, b)
            node.right = self.insert(node.right, c, d)
            node.startTime = b
            node.endTime = c
        return node  
```
#### Complexity Analysis
* Time Complexity: $O(n\log n)$ (Base Case) or $O(n^2)$ (Worst Case)
    * `insertable` method

        This method traverses the tree until a `null` location is found or range overlaps.
        * In the best case (when the tree is balanced), each recursive call halves the search space, leading to a logarithmic time complexity of $O(\log n)$.
        * In the worst case, the tree is like a linked list, resulting in a time complexity of $O(n)$.
    * `insert` method

        Similar to `insertable`, it has a time complexity of $O(\log n)$ or $O(n)$.

    The overall time complexity for `n` bookings is $O(n\log n)$ (Base Case) or $O(n^2)$ (Worst Case).

* Space Complexity: $O(n)$

    Each successful `book` allocates `SegmentTree` nodes, so after $n$ bookings the tree stores $O(n)$ nodes.  
    The recursion stack adds $O(\log n)$ depth in the balanced case and $O(n)$ when the tree degenerates into a linked list, which does not change the $O(n)$ bound.

    Thus, the overall space complexity is $O(n)$.
#### Java Implementation
```java
class MyCalendarTwo {
    class SegmentTree {
        int start, end;
        boolean overlap; 
        SegmentTree left, right; 
        SegmentTree(int start, int end) {
            this.start = start;
            this.end = end;
        }
    }

    SegmentTree root;

    public MyCalendarTwo() {
    }

    public boolean book(int start, int end) {
        if (!insertable(start, end, root)) 
            return false;
        
        root = insert(start, end, root);
        return true;
    }

    /**
     * Check the location of the current range relative to the `node`.
     */
    private boolean insertable(int start, int end, SegmentTree node) {
        if (start >= end) return true;
        if (node == null) return true;
        if (start >= node.end) { 
            // Check right side
            return insertable(start, end, node.right);
        } else if (end <= node.start) { 
            // check left side
            return insertable(start, end, node.left);
        } else { 
            // Ignore the current range if the `node` node is already overlapped
            if (node.overlap) { 
                return false;
            } else { 
                // The current range from `start` to `end` is within the `node` range
                if (start >= node.start && end <= node.end) { 
                    return true;
                } else { 
                    // Check left and right side
                    return insertable(start, node.start, node.left) && insertable(node.end, end, node.right);
                }
            }
        }
    }
    
    /**
     * Insert the current range to a `null` location or split it if overlapping.
     */
    private SegmentTree insert(int start, int end, SegmentTree node) {
        if (start >= end) 
            return node;
        if (node == null) 
            return new SegmentTree(start, end);
        
        if (start >= node.end) { 
            // The current range is positioned to right of the `node` node
            node.right = insert(start, end, node.right);
        } else if (end <= node.start) { 
            // The current range is positioned to left of the `node` node
            node.left = insert(start, end, node.left);
        } else {
            node.overlap = true;
            //    L1   R1        start, end
            //      L2    R2     node.start, node.end
            // [L1, L2] - [L2, R1] - [R1,R2]

            int a = Math.min(node.start, start);
            int b = Math.max(node.start, start);

            int c = Math.min(node.end, end);
            int d = Math.max(node.end, end);
            // Range1: [a,b] 
            // Range2: [b,c] 
            // Range3: [c,d] 

            // One of the ranges [a,b] or [c,d] must overlap with `node`.
            // and it will continue to be compared with a neighboring node of `node`.
            node.left = insert(a, b, node.left);
            node.right = insert(c, d, node.right);
            
            node.start = b;
            node.end = c;
        }
        
        return node;
    }
}
```
#### Complexity Analysis
* Time Complexity: $O(n\log n)$ (Base Case) or $O(n^2)$ (Worst Case)
    * `insertable` method

        This method traverses the tree until a `null` location is found or range overlaps.
        * In the best case (when the tree is balanced), each recursive call halves the search space, leading to a logarithmic time complexity of $O(\log n)$.
        * In the worst case, the tree is like a linked list, resulting in a time complexity of $O(n)$.
    * `insert` method

        Similar to `insertable`, it has a time complexity of $O(\log n)$ or $O(n)$.

    The overall time complexity for `n` bookings is $O(n\log n)$ (Base Case) or $O(n^2)$ (Worst Case).

* Space Complexity: $O(n)$

    Each successful `book` allocates `SegmentTree` nodes, so after $n$ bookings the tree stores $O(n)$ nodes.  
    The recursion stack adds $O(\log n)$ depth in the balanced case and $O(n)$ when the tree degenerates into a linked list, which does not change the $O(n)$ bound.

    Thus, the overall space complexity is $O(n)$.