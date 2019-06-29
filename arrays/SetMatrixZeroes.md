## Set Matrix Zeroes

>  Given a *m* x *n* matrix, if an element is 0, set its entire row and column to 0. Do it [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm).
>
>  Solution signature: `setZeroes(int[][] matrix)`
>
>  [Try it on LeetCode.](https://leetcode.com/explore/interview/card/top-interview-questions-medium/103/array-and-strings/777/)



### Strategy

There is a number of ways to approach this question. A fundamental thing to realise here, is that you cannot simply have at it and set entire rows and columns to zero, whenever zero cell is encountered. Doing so will make it possible for the matrix scan to detect zeros brought about by the "zerofying" process itself, rather than detect only zeros which were originally present in the input matrix. 

The rows and columns we wish to "zerofy" need to be stored and processed once we are done scanning the entire matrix. This can be done, for example, using lists which will result in an *O(m+n)* space complexity solution. Note that we do not have to keep track of all the *cells* that were zero, only their corresponding *rows* and *cols*. There's at most *m* rows and at most *n* columns, hence the space we will need is *O(m+n)*.

The die hard solution is actually *O(1)* space, and it requires taking advantage of input matrix as a place to store data. 

1. The trick is to identify the first zero cell, and keep its column and row as our storage place. The columns and rows of any future zero cells will be marked in this special storage place. That is, upon encountering any subsequent cell such that `(row, col) == 0` we will set:   

   * `matrix[specialRow][col] = 0`, and

   * `matrix[row][specialCol] = 0`
   
   Note that if no special row/col were found it means no zeros were encountered and our work is done.
   
2. Then we will need to go over all items in `matrix[specialRow]` and `matrix[specialCol]` and actually set to zero whatever needs to be set to zero, that is, any row `i` such that `matrix[i][specialCol] = 0` and any col `j` such that `matrix[specialRow][j] = 0`.

   We need to make sure we don't accidentally overwrite the special row and column while we are processing them.

3. Finally, we need to set to zero the special row and col.

   

### Code

The most upvoted (Java) solution on LeetCode's discussion section solves the question in *O(1)* space using the approach described above.

```java
public void setZeroes(int[][] matrix) {
    boolean fr = false,fc = false;
    for(int i = 0; i < matrix.length; i++) {
        for(int j = 0; j < matrix[0].length; j++) {
            if(matrix[i][j] == 0) {
                if(i == 0) fr = true;
                if(j == 0) fc = true;
                matrix[0][j] = 0;
                matrix[i][0] = 0;
            }
        }
    }
    for(int i = 1; i < matrix.length; i++) {
        for(int j = 1; j < matrix[0].length; j++) {
            if(matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    }
    if(fr) {
        for(int j = 0; j < matrix[0].length; j++) {
            matrix[0][j] = 0;
        }
    }
    if(fc) {
        for(int i = 0; i < matrix.length; i++) {
            matrix[i][0] = 0;
        }
    }   
}
```



### Refactored

It is a non trivial task to correlate the steps we have described with the code presented above. It's difficult to identify what's being handled where.

How about now? Is it any easier to figure our what's being handled where?

```java
public void setZeroes(final int[][] matrix) {
  // step 1
  final int[] pivots = encodeZeros(matrix);

  if (pivots == null) {
    return;
  }

  final int pivotRow = pivots[0];
  final int pivotCol = pivots[1];

  // step 2
  processPivotRow(matrix, pivotRow, pivotCol);
  processPivotCol(matrix, pivotRow, pivotCol);

  // step 3
  makeZeroRow(matrix, pivotRow);
  makeZeroCol(matrix, pivotCol);
}
```

step 1 bookkeeping:

```java
private void encodeZerosInPivots(int[][] matrix, int[] pivots, int row, int col) {
  final int pivotRow = pivots[0];
  final int pivotCol = pivots[1];
  matrix[pivotRow][col] = 0;
  matrix[row][pivotCol] = 0;
}

private int[] encodeZeros(final int[][] matrix) {
  int[] pivots = null;

  for (int row = 0; row < matrix.length; row++) {
    for (int col = 0; col < matrix[0].length; col++) {
      if (matrix[row][col] == 0) {
        pivots = (pivots == null) ? new int[] { row, col } : pivots;
        encodeZerosInPivots(matrix, pivots, row, col);
      }
    }
  }
  
  return pivots;
}
```

step 2 bookkeeping:

```java
private void processPivotRow(int[][] matrix, int pivotRow, int pivotCol) {
  IntStream.range(0, matrix[0].length)
     // do not overwrite the pivotCol
    .filter(col -> col != pivotCol && matrix[pivotRow][col] == 0)
    .forEach(col -> makeZeroCol(matrix, col));
}

private void processPivotCol(int[][] matrix, int pivotRow, int pivotCol) {
  IntStream.range(0, matrix.length)
    // do not overwrite the pivotRow
    .filter(row -> row != pivotRow && matrix[row][pivotCol] == 0)
    .forEach(row -> makeZeroRow(matrix, row));
}
```

and finally, step 3 bookkeeping:

```java
private void makeZeroRow(final int[][] matrix, final int k) {
  for (int n = 0; n < matrix[0].length; n++) {
    matrix[k][n] = 0;
  }
}

private void makeZeroCol(final int[][] matrix, final int k) {
  for (int n = 0; n < matrix.length; n++) {
    matrix[n][k] = 0;
  }
}
```

