## Valid Sudoku

> Determine if a 9x9 Sudoku board is valid. Only the filled cells need to be validated **according to the following rules**:
>
> 1. Each row must contain the digits `1-9` without repetition.
>
> 2. Each column must contain the digits `1-9` without repetition.
>
> 3. Each of the 9 `3x3` sub-boxes of the grid must contain the digits `1-9` without repetition
>
> Solution signature: `boolean isValidSudoku(char[][] board)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/valid-sudoku/)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)



### Strategy

We need to implement the checks mandated by the Sudoku rules. Perhaps the only trick is to keep calm and avoid bookkeeping errors, which may not be very trivial since there's a lot of bookkeeping going on here.



### Code

A very upvoted (Java) solution I saw online was as follows:

```java
// a solution (seen online)
public boolean isValidSudoku(char[][] board) {
    for(int i = 0; i<9; i++){
        HashSet<Character> rows = new HashSet<Character>();
        HashSet<Character> columns = new HashSet<Character>();
        HashSet<Character> cube = new HashSet<Character>();
        for (int j = 0; j < 9;j++){
            if(board[i][j]!='.' && !rows.add(board[i][j]))
                return false;
            if(board[j][i]!='.' && !columns.add(board[j][i]))
                return false;
            int RowIndex = 3*(i/3);
            int ColIndex = 3*(i%3);
            if(board[RowIndex + j/3][ColIndex + j%3]!='.' && !cube.add(board[RowIndex + j/3][ColIndex + j%3]))
                return false;
        }
    }
    return true;
}
```

If I were to review this code the following pops out (in descending order of importance):

- `if(board[RowIndex + j/3][ColIndex + j%3]!='.' && !cube.add(board[RowIndex + j/3][ColIndex + j%3]))` is a complex condition, and I will most definitely have to spend time compiling it in my head. The same applies to another couple of `if` clauses in the body of the inner loop

* Personally I'm not a big fan of side effects inside conditional expressions such as `if(... && !rows.add(board[i][j]))`. This creates a situation where the evaluation of the conditional expression also mutates the `rows` data structure, which is not very intuitive.
* `board[i][j]!='.'` repeats multiple times, which is error prone and may accidentally be mixed with, say, `board[j][i]!='.'` which also appears in the code.
* The literal `9` is used as a magic number. I'm not a Sudoku expert, so maybe there are no boards other than `9x9`, but at the very least this could be a constant, e.g. `SUDOKU_BOARD_SIZE` or the likes
* `int RowIndex` is a variable starting with a capital letter, but that's my OCD speaking
* `HashSet<Character> rows = new HashSet<Character>()` would ideally be `Set<Character> rows = new HashSet<Character>()`. In this particular case these are local variables so no harm done, but in other cases this could lead to leaking the exact type of collection that is internally used in this method. Ideally we should limit the client's knowledge of such details to the bare minimum the most upper parent of the `HashSet` class.



### Refactored

Let's see if we can make some changes to make things clearer.

If we go back to the rules and naively translate them into code we get something like so:

```java
public boolean isValidSudoku(char[][] board) {
  return validateRows(board) && validateCols(board) && validateSubBoxes(board);
}
```

Before we dive into implementing all these methods, an observation we can make is that rows, columns, and internal sub-boxes are *all special cases of a box*. Rows are boxes of height `1` and width `n`, columns are the other way around, and all the sub-boxes are `3x3` boxes.

This suggests that if we know how to validate an arbitrary sized box, we can use same method for all three cases. Let's do just that.

In order to validate a given box we need to scan its cells one by one and inspect the content. We are allowed to have only one instance of each non-empty cell we encounter. If we see a cell that violates this rule, we immediately return `false` to indicate the box is invalid. Otherwise, we keep scanning the rest of the box until we either encounter a cell that violates the rules, or, we complete the scanning. The latter means the box is valid (since we were not able to find a violation).

```java
private boolean isValidBox(char[][] board, 
                           int rowStart, 
                           int rowEnd, 
                           int colStart, 
                           int colEnd) {
  Set<Character> seen = new HashSet<>();
  for (int row = rowStart; row <= rowEnd; row++) {
    for (int col = colStart; col <= colEnd; col++) {
				char cell = board[row][col];
        if (nonEmpty(cell) && seen.contains(cell)) {
          return false;
        } else if (nonEmpty(cell)) {
          seen.add(cell);
        }
    }
  }
  return true;
}
```

I am hardly a fan of nested loops, but since in this case we wish to scan *only a particular range* of cells within a two dimensional array, loops seem like the best choice. What we can do, is to avoid making the code more convoluted than it has to be, for example by keeping variable and auxiliary method names intuitive. 

Instead of using `board[i][j]` everywhere we name the loop variables `row` and `col`, and extract the currently examined cell to a variable we call `cell` instead of directly specifying `board[row[col]` every time we need it. Instead of repeating `cell != '.'` in multiple places we extract it to a method and use `nonEmpty(cell)` .

Once we have the major building block of being able to validate an arbitrary sized box, we just need to utilize it in order perform the checks specified earlier:

```java
validateRows(board) && validateCols(board) && validateSubBoxes(board);
```

`validateRows` and `validateCols` are quite straight forward:

```java
private boolean validateRows(char[][] board) {
  return IntStream.range(0, board.length) // a fancy for loop
    .allMatch(row -> isValidBox(board, row, row, 0, board[0].length - 1));
}

private boolean validateCols(char[][] board) {
  return IntStream.range(0, board.length) // a fancy for loop
    .allMatch(col -> isValidBox(board, 0, board.length - 1, col, col));
}
```
`validateSubBoxes` is a bit more complex. 

We have 9 boxes to check, let's index them 0-9. To make use of the `isValidBox` method we devise we need to translate each individual box index to the corresponding `rowStart`, `rowEnd`, `colStart`, `coldEnd` parameters.

```java
private int[] toBoxCoordinates(int boxIndex) {
  int colStart = (boxIndex % 3) * 3;
  int colEnd = colStart + 2;

  int rowStart = (boxIndex / 3) * 3;
  int rowEnd = rowStart + 2;

  return new int[] {rowStart, rowEnd, colStart, colEnd};
}
```

Once we know how to translate box indices to box 'coordinates' it's just a matter of glueing the pieces together:

```java
private boolean validateSubBoxes(char[][] board) {        
  for(int box = 0; box < board.length; box++) {         
    int[] boxCoordinates = toBoxCoordinates(box);
    if(!isValidBox(board,
                   boxCoordinates[0],
                   boxCoordinates[1],
                   boxCoordinates[2],
                   boxCoordinates[3])) {
      return false;
    }
  }
  return true;
}

```

Or if you are really (really) into Java 8 streams:

```java
private boolean validateSubBoxes(char[][] board) {
  return IntStream.rangeClosed(0, board.length - 1)
    .mapToObj(this::toBoxCoordinates)
    .allMatch(coordinates -> isValidBox(board,
                                        coordinates[0],
                                        coordinates[1],
                                        coordinates[2],
                                        coordinates[3]));
}
```



#### Caveats

* Make sure you don't add the `.` character to the `seen` char Set (as there can be multiple `.` characters in a valid row)

* If you are afraid you might forget the conversion formulas from box index to box coordinates you can also hardcode it since it's only 9 boxes:

  ```java
  int[][] boxes = new int[][]{ 
    // {rowStart, rowEnd, colStart, colEnd}, inclusive
    {0,2,0,2}, {0,2,3,5}, {0,2,6,8},
    {3,5,0,2}, {3,5,3,5}, {3,5,6,8}, 
    {6,8,0,2}, {6,8,3,5}, {6,8,6,8}
  };
  ```