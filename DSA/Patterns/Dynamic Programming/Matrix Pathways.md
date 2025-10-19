# Matrix Pathways

You are positioned at the top-left corner of a m x n matrix, and can only move downward or rightward through the matrix. Determine the number of unique pathways you can take to reach the bottom-right corner of the matrix.

Input: m = 3, n = 3
Output: 6

Constraints:
• m, n >= 1

### Intuition

Because we know the number of paths to our current cells is the culmination of the number of paths it takes to get to the position direct above and directly to the left.

So now we have our recurrence relation, which is
f(i,j) = f(i[0,-1] + j[-1,0])

We know for the base cases aswell, that for dp[0][0] that it should equal to 1.

From this, we can populate the dp table, we can choose to fill in the top row and left column with 1's, or have an if statement to manage it.

```java
    public int matrix_pathways(int m, int n) {
        int dp = new int[n][m];
        dp[0][0] = 1;

        for(int i = 0; i < m; i++){
            for(int j = 0; j < n; j++){
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }

        return dp[m - 1][n - 1];
    }
```

### Complexity

TC: O(m _ n)
SC: O(m _ n)

### Optimization

Because we only need the top and the left value, we can actually optimize the space to be the row above and the current row, updating the prev row to the current when the index completes a row.

```python
def matrix_pathways_optimized(m: int, n : int) - > int:
    # Initialize ' prev_row' as the DP values of row e, which are all ls,
    prev_row = [1] * n
    # Iterate through the matrix starting from row 1.
    for r in range(l, m):
        # Set the first cell of 'curr_row' to 1 . This is done by
        # setting the entire row to 1 .
        curr_row = [1] * n
        for c in range(l, n):
            # The number of unique pat:hs to the current cell is the su,
            # of the paths from the eel l above it ( 'prev_row[ c) ') and
            # the cell to the left ('curr_row[c - 1)').
            curr_row[c] = prev_row[c] + curr_row[c - 1]
            # Update 'prev_row' with 'curr_row' values for the next
            # iteration.
            prev_row = curr_row
            # The last element in 'prev_row' stores the result for the
            # bottom-right cell.
    return prev_row[n - 1]
```
