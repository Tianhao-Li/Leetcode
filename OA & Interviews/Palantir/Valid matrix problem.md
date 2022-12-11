# Valid matrix problem

## 1. Is matrix valid

> 给一个N*N的矩阵，判定是否是有效的矩阵。有效矩阵的定义是每一行或者每一列的数字都必须正好是1到N的数。输出一个bool

- Input clarification

```java
public boolean isValidMatrix(int[][] matrix) {
    // Idea: Use a List to store each column and row's info, maintain a max&min to check the range
    // Corner case: ...

    List<Set<Integer>> rows = new ArrayList<>();
    List<Set<Integer>> cols = new ArrayList<>();
    List<Integer> rowMins = new ArrayList<>();
    List<Integer> rowMaxs = new ArrayList<>();
    List<Integer> colMins = new ArrayList<>();
    List<Integer> colMaxs = new ArrayList<>();
    int n = matrix.length;
    // Init Lists
    for (int index = 0; index < n; index++) {
        rows.add(new HashSet<>());
        cols.add(new HashSet<>());
        rowMins.add(Integer.MAX_VALUE);
        rowMaxs.add(Integer.MIN_VALUE);
        colMins.add(Integer.MAX_VALUE);
        colMaxs.add(Integer.MIN_VALUE);
    }

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            int num = matrix[i][j];
            // Check row
            if (rows.get(i).contains(num)) {
                return false;
            } else {
                rows.get(i).add(num);
                rowMins.set(i, Math.min(rowMins.get(i), num));
                rowMaxs.set(i, Math.max(rowMaxs.get(i), num));
            }
            // Check column
            if (cols.get(j).contains(num)) {
                return false;
            } else {
                cols.get(j).add(num);
                colMins.set(j, Math.min(colMins.get(j), num));
                colMaxs.set(j, Math.max(colMaxs.get(j), num));
            }
        }
    }

    // Check range
    for (int index = 0; index < n; index++) {
        if (rowMins.get(index) != 1 || colMins.get(index) != 1 || rowMaxs.get(index) != n || colMaxs.get(index) != n) {
            return false;
        }
    }

    return true;
}
```



## 2. Is Nonogram valid

- Input size clarification

```java
// (2) Nonogram // 0 - balck, 1 - white
public boolean isValidNonogram(int[][] matrix, List<List<Integer>> rows, List<List<Integer>> cols) {
    // Idea: validate rows & columns separately
    // Corner case: ...

    int m = matrix.length;
    int n = matrix[0].length;

    return validRow(matrix, rows, m, n) && validCol(matrix, cols, m, n);
}

// Helper function for validating rows/cols
private boolean validRow(int[][] matrix, List<List<Integer>> instructions, int m, int n) {
    // Scan each row
    for (int i = 0; i < m; i++) {
        List<Integer> instruct = instructions.get(i);
        int instructIdx = 0;
        // For each element in this row
        for (int j = 0; j < n; j++) {
            // Found a part to validate
            if (matrix[i][j] == 0) {
                // case 1: no instruct
                if (instruct.size() == 0) {
                    return false;
                }
                // case 2: validate consecutive numbers given the instruct
                for (int k = 0; k < instruct.get(instructIdx); k++) {
                    if (j + k >= n || matrix[i][j + k] != 0) {
                        return false;
                    }
                }
                // then move j to the next value after consecutive 0s
                j += instruct.get(instructIdx);
                instructIdx++;
            }
        }
        // Check if we have matched all the patterns in the instruction
        if (instructIdx != instruct.size()) {
            return false;
        }
    }
    return true; // validation ended
}

private boolean validCol(int[][] matrix, List<List<Integer>> instructions, int m, int n) {
    // Scan each col
    for (int j = 0; j < n; j++) {
        List<Integer> instruct = instructions.get(j);
        int instructIdx = 0;
        // For each element in this col
        for (int i = 0; i < m; i++) {
            // Found a part to validate
            if (matrix[i][j] == 0) {
                // case 1: no instruct
                if (instruct.size() == 0) {
                    return false;
                }
                // case 2: validate consecutive numbers given the instruction
                for (int k = 0; k < instruct.get(instructIdx); k++) {
                    if (i + k >= m || matrix[i + k][j] != 0) {
                        return false;
                    }
                }
                // then move i to the next value after consecutive 0s
                i += instruct.get(instructIdx);
                instructIdx++;
            }
        }
        // Check if we have matched all the patterns in the instruction
        if (instructIdx != instruct.size()) {
            return false;
        }
    }
    return true; // validation ended
}
```