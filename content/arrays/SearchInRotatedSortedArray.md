## Search in Rotated Sorted Array

> Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.
>
> (i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).
>
> You are given a target value to search. If found in the array return its index, otherwise return `-1`.
>
> You may assume no duplicate exists in the array.
>
> Your algorithm's runtime complexity must be in the order of *O*(log *n*).
>
> **Example 1:**
>
> ```
> Input: nums = [4,5,6,7,0,1,2], target = 0
> Output: 4
> ```
>
> **Example 2:**
>
> ```
> Input: nums = [4,5,6,7,0,1,2], target = 3
> Output: -1
> ```
>
> Solution signature: `int search(int[] nums, int target)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/search-in-rotated-sorted-array/)



### Strategy

Our approach will be a tweaked binary search. As usual, we begin with cutting the array in half, and inspecting the middle element. Since the input array has been rotated, either the left half, or the right half are sorted, therefore, either `[start, middle - 1)` or `[middle + 1, end]` are sorted. We can check if a given subarray is sorted by checking its first and last elements, `start < end` means it's sorted, otherwise it's not. 

Each time we cut the array in half we rule out on of the halves. We pick the sorted half and check whether the target element should be in that half, if at all. If it should, we focus on that half and search further, if it shouldn't, we focus on the other (non sorted) half of the array and search it for the target element. 

Either way we reduce the search space by a factor of 2 each iteration, so the time complexity will eventually be *O(logn)*.



### Code

```c++
int search(int A[], int n, int target) {
  int lo=0, hi=n-1;
  // find the index of the smallest value using binary search.
  // Loop will terminate since mid < hi, and lo or hi will shrink by at least 1.
  // Proof by contradiction that mid < hi: if mid==hi, then lo==hi and loop would have been 
  // terminated.
  while(lo<hi){
    int mid=(lo+hi)/2;
    if(A[mid]>A[hi]) lo=mid+1;
    else hi=mid;
  }
  // lo==hi is the index of the smallest value and also the number of places rotated.
  int rot=lo;
  lo=0;hi=n-1;
  // The usual binary search and accounting for rotation.
  while(lo<=hi){
    int mid=(lo+hi)/2;
    int realmid=(mid+rot)%n;
    if(A[realmid]==target)return realmid;
    if(A[realmid]<target)lo=mid+1;
    else hi=mid-1;
  }
  return -1;
}
```

The idea sounds cleve, but far from being straight forward. The code structure does not really help with understanding things, and if it weren't for the comments it'd be very hard to figure it out. Even after having read the comments, performing a binary search while accounting for the rotation is not for the faint hearted.

We next try to come up with an intuitive, and far less sophisticated solution (which is IMHO, can actually be a good thing).



### Refactored

```java
public int search(int[] nums, int target) {
  return (nums == null || nums.length == 0) ? 
    -1 : 
    search(nums, target, 0, nums.length - 1);
}
```

```java
public int search(int[] nums, int target, int start, int end) {
  if (start == end) {
    return nums[start] == target ? start : -1;
  }

  int middle = start + (end - start) / 2;
  if (nums[middle] == target) {
    return middle;
  }
  int[] nextSearchRange = buildSearchRange(nums, target, start, end, middle);
  return search(nums, target, nextSearchRange[0], nextSearchRange[1]);
}
```

```java
private int[] buildSearchRange(int[] nums, int target, int start, int end, int pivot) {
  int leftOfPivot = Math.max(pivot - 1, start);
  int rightOfPivot = Math.min(pivot + 1, end);

  int[] range;

  if (isSorted(nums, start, leftOfPivot)) {
    range =
      isInSortedRange(target, nums, start, leftOfPivot) ? 
      new int[] {start, leftOfPivot} : 
      new int[] {rightOfPivot, end};
  } else {
    range =
      isInSortedRange(target, nums, rightOfPivot, end) ?
      new int[] {rightOfPivot, end} :
      new int[] {start, leftOfPivot};
  }

  return range;
}
```

```java
// this is not a general method to check if an array is sorted
// and it only applies to the particular use case in this question
private boolean isSorted(int[] nums, int start, int end) {
  return nums[start] <= nums[end];
}

private boolean isInSortedRange(int target, int[] nums, int start, int end) {
  return target >= nums[start] && target <= nums[end];
}
```

