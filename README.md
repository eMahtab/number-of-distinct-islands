# Number of distinct islands
## https://leetcode.com/problems/number-of-distinct-islands
## https://www.lintcode.com/problem/860/

Given a non-empty 2D array grid of 0's and 1's, an island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

Count the number of distinct islands. An island is considered to be the same as another if and only if one island can be translated (and not rotated or reflected) to equal the other.
```
Example 1:
11000
11000
00011
00011

Given the above grid map, return 1.

Example 2:
11011
10000
00001
11011

Given the above grid map, return 3.

Notice that:
11
1
and
 1
11
are considered different island shapes, because we do not consider reflection / rotation.
```
**Note: The length of each dimension in the given grid does not exceed 50.**

## Wrong Implementation :
```java
class Solution {
    public int numDistinctIslands(int[][] grid) {
        if(grid == null || grid.length == 0)
            return 0;
        Set<String> set = new HashSet<>();
        for(int i = 0; i < grid.length; i++) {
            for(int j = 0; j < grid[0].length; j++) {
                if(grid[i][j] == 1) {
                    StringBuilder path = new StringBuilder();
                    traverseIsland(grid, i, j, path, "S");
                    set.add(path.toString());
                }
            }
        }
        return set.size();
    }
    
    private void traverseIsland(int[][] grid, int row, int column, StringBuilder path, String move) {
        if(row < 0 || row >= grid.length || column < 0 || column >= grid[0].length || 
           grid[row][column] == 0)
            return;
        
        path.append(move);
        grid[row][column] = 0;
        traverseIsland(grid, row, column + 1, path, "R");
        traverseIsland(grid, row, column - 1, path, "L");
        traverseIsland(grid, row - 1, column, path, "U");
        traverseIsland(grid, row + 1, column, path, "D");
    }
}
```

There is one mistake in above implementation, the path for these two different island shapes 
![Island Shape 1](island-shape-1.JPG?raw=true "Island Shape 1") and ![Island Shape 2](island-shape-2.JPG?raw=true "Island Shape 2") are same, in above implementation, which is (SRD). But these are two different islands.

**Solution :**

So to solve this issue, we need to make sure that the path constructed by these 2 islands are different.
We can do that, by appending "X" at the end, once we exhausted going in all 4 directions (right, left, up, down), from a cell.
By doing so the path for the first island shape will be `SRXDXX` and the path for the second island shape will be `SRDXXX`

## Correct Implementation : Time : O(rows * columns)  , Space : O(rows * columns)

## Approach 1 : DFS
```java
class Solution {
    public int numDistinctIslands(int[][] grid) {
        if(grid == null || grid.length == 0)
            return 0;
        Set<String> set = new HashSet<>();
        for(int i = 0; i < grid.length; i++) {
            for(int j = 0; j < grid[0].length; j++) {
                if(grid[i][j] == 1) {
                    StringBuilder path = new StringBuilder();
                    traverseIsland(grid, i, j, path, "S");
                    set.add(path.toString());
                }
            }
        }
        return set.size();
    }
    
    private void traverseIsland(int[][] grid, int row, int column, StringBuilder path, String move) {
        if(row < 0 || row >= grid.length || column < 0 || column >= grid[0].length || 
           grid[row][column] == 0)
            return;
        
        path.append(move);
        grid[row][column] = 0;
        traverseIsland(grid, row, column + 1, path, "R");
        traverseIsland(grid, row, column - 1, path, "L");
        traverseIsland(grid, row - 1, column, path, "U");
        traverseIsland(grid, row + 1, column, path, "D");
        path.append("X"); // Appending X at the end
    }
}
```

## Approach 2 : BFS
```java
class Solution {
    public int numDistinctIslands(int[][] grid) {
      if(grid == null || grid.length == 0)
            return 0;

      Set<String> set = new HashSet<>();
      int rows = grid.length;
      int cols = grid[0].length;
      Queue<int[]> q = new LinkedList<>();
      Map<String,int[]> directions = new HashMap<>();
      directions.put("R", new int[]{0, 1});
      directions.put("L", new int[]{0, -1});
      directions.put("U", new int[]{-1, 0});
      directions.put("D", new int[]{1, 0});
      for(int i = 0; i < rows; i++) {
        for(int j = 0; j < cols; j++) {
          if(grid[i][j] == 1) {
             q.add(new int[]{i, j});
             grid[i][j] = 0;
             StringBuilder path = new StringBuilder("S");
             while(!q.isEmpty()) {
              int[] pos = q.remove();
              for(String direction : directions.keySet()) {
               int row = pos[0] + directions.get(direction)[0];
               int col = pos[1] + directions.get(direction)[1];
               if(row >= 0 && row < rows && col >= 0 && col < cols && grid[row][col] == 1) {
                 path.append(direction);
                 grid[row][col] = 0;	
                 q.add(new int[]{row, col});  
               }
               path.append("X");   
              }
             }
             set.add(path.toString()); 
          }
       }
     }

     return set.size(); 	 
   }
}
```


# References :
1. https://www.youtube.com/watch?v=c1ZxUOHlulo
2. https://leetcode.com/problems/number-of-distinct-islands/discuss/642897/Java-Solution-approach-similar-to-total-islands

