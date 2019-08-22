## Move Zeroes

>Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.
>
>Solution signature: `void moveZeroes(int[] nums) `
>
>[Try it on LeetCode.](https://leetcode.com/problems/move-zeroes/)



### Strategy

We'll need to scan the input array while keeping track of first "free" index where we can insert the next non zero element we encounter. In this context, free is where a zero elements used to be.

When we're done scanning the array the range `[0,index)` will contain all the non zero elements. Therefore, we can fill the range `[index, end)` with zeros to end up with an array where all the zero elements have been moved to the end.



### Code

Following is a top voted solution from LeetCode:

```java
public void moveZeroes(int[] nums) {
  int i = 0;
  for (int n : nums) {
    if (n != 0) {
      nums[i++] = n;
    }	         
  }
  while (i < nums.length) {
    nums[i++] = 0;
  }
}
```

It's clean indeed, but could use a little structure.



### Refactored

```java
public void moveZeroes(int[] nums) {  
  int nonZeroBoundary = moveNonZeroesToStart(nums); // check
  fillZeroes(nonZeroBoundary, nums); // mate
}
```

`moveNonZeroesToStart` takes care of moving all non zero integers to the start of the array, and returning the first index after the last non zero integer:

```java
private int moveNonZeroesToStart(int[] nums) {
  int nonZeroIndex = 0;
  for (int num : nums) {
    if (num != 0) {
      nums[nonZeroIndex++] = num;
    }
  }
  return nonZeroIndex;
}
```

`fillZeroes` makes sure to fill the remainder of the array with zeros, like so:

```java
private void fillZeroes(int start, int[] nums) {
  for (int i = start; i < nums.length; i++) {
    nums[i] = 0;
  }
}
```

