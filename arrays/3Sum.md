## 3Sum

> Given an array `nums` of *n* integers, are there elements *a*, *b*, *c* in `nums` such that *a* + *b* + *c* = 0? Find all unique triplets in the array which gives the sum of zero.
>
> **Note:** The solution set must not contain duplicate triplets.
>
> Solution signature: `List<List<Integer>> threeSum(int[] nums)`
>
> [Try it on LeetCode](https://leetcode.com/explore/interview/card/top-interview-questions-medium/103/array-and-strings/776/).



### Strategy

Since we need unique triplets, we need to make sure we skip/exclude triplets we already have. One way to achieve this would be to consider triplets which *start* with the number `n` for each a given number `n` in the input array. Note that a triplet `3,-2,-1` and the triplet `3,-1,-2` both start with `3` but are non unique and we should include only one of them. 

How can we avoid including such duplicates in the final answer?

By sorting the array before we process it. 

This way we impose an order according to which given a number `n` we consider all unique triplets that *start* with `n`. Any triplets consisting of the same numbers where `n` is not the first, are simply duplicates so we need must include them in the final answer. This is exactly what sorting gives us. By processing a sorted array, once we're done considering triplets starting with a given `n`, the next triplet(s) we consider should not (!) being with `n` because we've just finished considering that case.

For example, if the input array is `-1,1,0,2,-1,-1` sorting it will result in `-1,-1,-1,0,1,2`. Once we are done considering the `-1` at index 0 have the triplets: `-1,-1,2`, `-1,0,1`. Therefore, we should next advance straight index 3, and skip the `-1`'s at index 1 and 2, because triplets starting with `-1` have already been discovered and processed.

In order to compute triplets starting with `n`, we need to search for two numbers (two sum) that sum to `-n`. We take advantage of the fact the array has been sorted, and to find pairs of numbers which sum up to `-n` we only have to scan the relevant part of the array once. This is done by having an index going left from a given point in the array, and an index going left from the end of the array. If the sum is less than the target (i.e., `-n`) we advance the left index (so increase the sum), if the sum is greater than the target in decrease the right index (so decrease the sum). If the sum is exactly the target we move both indices in their corresponding directions. The last point to note here, is that when we say "advance" to the left or right, it should be done by skipping any duplicates.

The time complexity is *O(n^2)*. First we sort the array which costs *O(nlogn)* time, then we go over every index in the input array and scan its right hand side once which amounts to `(n-1) + (n-2) + (n-3) + … + 1 = (n(n-2))/2 ~ O(n^2)`. The dominating component is *O(nlogn + n^2) = O(n^2)*. Space complexity is *O(n)*, as required by the sorting algorithm.

You would assume that once we cracked through the strategy the hard part was over. Unfortunately that is not the case. On to the coding part.



### Code

The most upvoted solution on CodeLeet looks like so:

```java
public List<List<Integer>> threeSum(int[] num) {
  Arrays.sort(num);
  List<List<Integer>> res = new LinkedList<>(); 
  for (int i = 0; i < num.length-2; i++) {
    if (i == 0 || (i > 0 && num[i] != num[i-1])) {
      int lo = i+1, hi = num.length-1, sum = 0 - num[i];
      while (lo < hi) {
        if (num[lo] + num[hi] == sum) {
          res.add(Arrays.asList(num[i], num[lo], num[hi]));
          while (lo < hi && num[lo] == num[lo+1]) lo++;
          while (lo < hi && num[hi] == num[hi-1]) hi--;
          lo++; hi--;
        } else if (num[lo] + num[hi] < sum) lo++;
        else hi--;
      }
    }
  }
	return res;
}
```

It's a master piece, a delicate tango of nested loops and conditional flows.

Kinda hard to actually figure out what's going on though. Even after we are familiar with the strategy. Even after you stare at for a couple of minutes.



### Refactored

Following the devised strategy the blue print goes like so:

```java
public List<List<Integer>> threeSum(int[] nums) {

	// validate triplet are even possible
  if (nums.length < 3) {
    return new ArrayList<>();
  }

  // sort the array
  Arrays.sort(nums);

  List<List<Integer>> threeSums = new ArrayList<>();

  // go over the sorted array's items, while skipping any repeating ones
  // for each index try building triplets starting with its value in the array
  for (int i = 0; i < nums.length; i = skipRepeatsForward(i, nums)) {
    threeSums.addAll(threeSumsForIndex(i, nums));
  }
  
  return threeSums;
}
```

Having this blue print in mind, we're left with implementing the helper methods.

```java
// given a start index, return the index of the next non repeating value on the right
private int skipRepeatsForward(int start, int[] nums) {
  int next = start + 1;
  while (next < nums.length && nums[next] == nums[next - 1]) {
    next++;
  }
  return next;
}
```

In order to compute a triplet starting with the value at index `i` we need to figure out `threeSumsForIndex`:

```java
private List<List<Integer>> threeSumsForIndex(int i, int[] nums) {
  int num = nums[i];
  List<List<Integer>> twoSums = searchTwoSum(i + 1, nums, -num);
  return twoSums.stream()
    .map(twoSum -> make3Sum(num, twoSum))
    .collect(Collectors.toList());
}
```

`make3Sum` is just a convenience method for turning tuples into triplets:

```java
private List<Integer> make3Sum(int num, List<Integer> twoSum) {
  return Arrays.asList(num, twoSum.get(0), twoSum.get(1));
}
```

`searchTwoSum` makes use of the fact the array has been sorted:

```java
private List<List<Integer>> searchTwoSum(int start, int[] nums, int target) {
  List<List<Integer>> twoSums = new ArrayList<>();
  int end = nums.length - 1;
  while (start < end) {
    int sum = nums[start] + nums[end];
    if (sum < target) {
      start = skipRepeatsForward(start, nums);
    } else if (sum > target) {
      end = skipRepeatsBackward(end, nums);
    } else {
      twoSums.add(Arrays.asList(nums[start], nums[end]));
      start = skipRepeatsForward(start, nums);
      end = skipRepeatsBackward(end, nums);
    }
  }
  return twoSums;
}
```

similarly to `skipRepeatsForward`,  `skipRepeatsForward` does the same only from the end to the left:

```java
// given a start index, return the index of the next non repeating value on the left
private int skipRepeatsBackward(int end, int[] nums) {
  int prev = end - 1;
  while (prev > -1 && nums[prev] == nums[prev + 1]) {
    prev--;
  }
  return prev;
}
```


