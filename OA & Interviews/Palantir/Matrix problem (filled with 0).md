# Matrix problem

## 1. Find the rectangle filled with 0

> There is at most one rectangle in this image filled with 0s, find the rectangle. Output could be the coordinates of top-left and bottom-right elements of the rectangle, or top-left element, width and height.

- Confirm the definition of rectangle , length or width 1 also counts?

```java
public int[][] findOneRectangle(int[][] board) {
    // Idea: Since there's only 1 rectangle of 0, all 0 must be together
    // Corner case:
    if (board == null || board.length == 0 || board[0].length == 0) {
        return null;
    }

    int[][] result = new int[2][2];
    int m = board.length;
    int n = board[0].length;

    // Look for starting point of the array
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 0) {
                // Found the starting point
                result[0][0] = i;
                result[0][1] = j;
                // Then expand to look for the ending point
                int height = 1;
                int width = 1;
                while (i + height < m && board[i + height][j] == 0) {
                    height++;
                }
                while (j + width < n && board[i][j + width] == 0) {
                    width++;
                }
                // add bottom right corner to result then break
                result[1][0] = i + height - 1;
                result[1][1] = j + width - 1;
                break;
            }
        }
        // We need to break again here to exit the i loop
        if (result[1][0] != 0 || result[1][1] != 0) {
            break;
        }
    }

    return result;
}
```



## 2. Find multiple rectangles

> it is filled with 0s and 1s. It may have multiple rectangles filled with 0s. The rectangles are separated by 1s. Find all the rectangles.

- Can I modify the input?

```java
public List<List<List<Integer>>> findMultipleRectangle(int[][] board) {
    // Idea: Similar to (1) to find a rectangle, then similar to find islands, modify this rectangle to be 1
    // Corner case: ...

    List<List<Integer>> topLefts = new ArrayList<>();
    List<List<Integer>> bottomRights = new ArrayList<>();
    int m = board.length;
    int n = board[0].length;

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 0) {
                System.out.print(i);
                System.out.println(j);
                // Found a top-left, add to list
                topLefts.add(Arrays.asList(i,j));
                // Expand to find the whole rectangle
                int height = 1; // # note that h&w will be 1 unit longer than rectangle h&2
                int width = 1;
                while (i + height < m && board[i + height][j] == 0) {
                    height++;
                }
                while (j + width < n && board[i][j + width] == 0) {
                    width++;
                }
                bottomRights.add(Arrays.asList(i + height - 1, j + width - 1));
                // Modify this area to be 1
                for (int row = i; row < i + height; row++) {
                    for (int col = j; col < j + width; col++) {
                        board[row][col] = 1;
                    }
                }
            }
        }
    }

    // Output
    List<List<List<Integer>>> result = new ArrayList<>();
    for (int index = 0; index < topLefts.size(); index++) {
        List<List<Integer>> rectangle = new ArrayList<>();
        rectangle.add(topLefts.get(index));
        rectangle.add(bottomRights.get(index));
        result.add(rectangle);
    }
    return result;
}
```



## 3. Find all shapes which are filled with 0

> the image has random shapes filled with 0s, separated by 1s. Find all the shapes. Each shape is represented by coordinates of all the elements inside

- Clarify the output format?

```java
public List<List<List<Integer>>> findMultipleShapes(int[][] board) {
    // Idea: traverse and find a 0, then use floodfill to find all adjacent 0s
    // Corner case: ...
    List<List<List<Integer>>> result = new ArrayList<>();
    int m = board.length;
    int n = board[0].length;

    // Copy matrix
    int[][] mat = new int[m][n];
    for (int row = 0; row < m; row ++) {
        for (int col = 0; col < n; col++) {
            mat[row][col] = board[row][col];
        }
    }

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (mat[i][j] == 0) {
                List<List<Integer>> points = new ArrayList<>();
                dfs(mat, i, j, points);
                result.add(new ArrayList<>(points));
            }
        }
    }

    return result;
}

// Helper function for floodfill dfs
private void dfs(int[][] mat, int x, int y, List<List<Integer>> points) {
    // Base case
    if (x < 0 || x >= mat.length || y < 0 || y >= mat[0].length || mat[x][y] == 1) {
        return; // invalid early return
    }
    // Add current point to "points" then modify the element
    points.add(Arrays.asList(x, y));
    mat[x][y] = 1;
    // Recursive call
    dfs(mat, x - 1, y, points);
    dfs(mat, x + 1, y, points);
    dfs(mat, x, y - 1, points);
    dfs(mat, x, y + 1, points);
}
```