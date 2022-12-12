# Find Treasure

## 1. Find legal moves

> Find the adjacent blocks that has 0 in it



## 2. If can reach every 0

> -1 is wall, 0 is road, given a starting 0, can we reach all zeros

- Clarify: Input assumptions? Can I make changes to the original matrix?
- Clarify: Reachability or reach all zeros in one path with no repeated visit to a block?

```java
public boolean findLegalMoves(int[][] matrix, int i, int j) {
    // Idea: use dfs (floodfill) to try to mark all the 0s
    // Corner case
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
        return false;
    }
    int m = matrix.length;
    int n = matrix[0].length;

    // Copy matrix
    int[][] mat = new int[m][n];
    for (int row = 0; row < m; row ++) {
        for (int col = 0; col < n; col++) {
            mat[row][col] = matrix[row][col];
        }
    }

    dfs(mat, i, j);

    // Check for result
    for (int r = 0; r < m; r++) {
        for (int c = 0; c < n; c++) {
            if (mat[r][c] == 0) {
                return false;
            }
        }
    }
    return true;
}

// Helper function for DFS
private void dfs(int[][] mat, int x, int y) {
    // Base case: out of bound, visited, can't pass
    if (x < 0 || x >= mat.length || y < 0 || y >= mat[0].length || mat[x][y] != 0) {
        return ;
    }
    // Change cur element to 1 to mark visited
    mat[x][y] = 1;
    // Recursive rule
    dfs(mat, x - 1, y);
    dfs(mat, x, y - 1);
    dfs(mat, x + 1, y);
    dfs(mat, x, y + 1);
}
```

- TC: O(mn)
- SC: O(mn) -- copying matrix, call stack worst case height is O(mn)



## 3. All shortest paths that get all treasure

> 1 is treasure, -1 is wall, 0 is road, given a start point and an end point, output all shortest paths that can take all treasures

- Clarify: Input assumption? 

```java
public List<List<List<Integer>>> findAllTreasures(int[][] board, List<Integer> start, List<Integer> end) {
    // Idea:
    // Corner case: ...
    List<List<List<Integer>>> paths = new ArrayList<>();

    // Count num of treasures
    int m = board.length;
    int n = board[0].length;
    int numTreasure = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 1) {
                numTreasure++;
            }
        }
    }

    // Copy matrix -- can just do it when counting treasures!
    int[][] mat = new int[m][n];
    for (int row = 0; row < m; row ++) {
        for (int col = 0; col < n; col++) {
            mat[row][col] = board[row][col];
        }
    }

    // DFS finds all path
    List<List<Integer>> path = new ArrayList<>();
    dfs(mat, start.get(0), start.get(1), end, numTreasure, path, paths);

    // Find the shortest path length
    int minLength = paths.get(0).size();
    for (List<List<Integer>> candidate : paths) {
        minLength = Math.min(minLength, candidate.size());
    }

    // Get all shortest paths
    List<List<List<Integer>>> result = new ArrayList<>();
    for (List<List<Integer>> candidate : paths) {
        if (candidate.size() == minLength) {
            result.add(candidate);
        }
    }

    return result;
}

// Helper function of finding treasure using DFS
private void dfs(int[][] mat, int x, int y, List<Integer> end, int remainTreasure, List<List<Integer>> path, List<List<List<Integer>>> result) {
    // Base case: out of bound, find invalid -1 or visited 2
    if (x < 0 || x >= mat.length || y < 0 || y >= mat[0].length || mat[x][y] == -1
            || mat[x][y] == 2) {
        return ;
    }

    // Operation at current node
    int temp = mat[x][y];
    if (temp == 1) {
        remainTreasure--;
    }
    // currently we are visiting a 0 or 1, add to path
    path.add(new ArrayList<>(Arrays.asList(x, y)));
    // modify cur element
    mat[x][y] = 2;
    // decide if we have reach the end of path
    if (x == end.get(0) && y == end.get(1) && remainTreasure == 0) {
        result.add(new ArrayList<>(path)); // do i need to make copy here?
        path.remove(path.size() - 1);
        mat[x][y] = temp;
        // early return
        return ;
    }

    // Recursive rule
    dfs(mat, x - 1, y, end, remainTreasure, path, result);
    dfs(mat, x + 1, y, end, remainTreasure, path, result);
    dfs(mat, x, y - 1, end, remainTreasure, path, result);
    dfs(mat, x, y + 1, end, remainTreasure, path, result);

    // Undo the changes
    path.remove(path.size() - 1);
    mat[x][y] = temp;
}
```

Testing

```java
int[] start = {5,2};
int[] end = {2,0};
List<List<List<Integer>>> result = test.findAllTreasures(board, start, end);

if (result.size() == 0) {
    System.out.println("None");
} else {
    for (List<List<Integer>> path : result) {
        for (List<Integer> node : path) {
            System.out.print(Arrays.toString(node.toArray()));
        }
        System.out.println();
    }
}
```



- TC: O(3^mn)
- SC: O(m^2n^2) -- call stack O(mn), length of path O(mn), number of paths around O(mn)