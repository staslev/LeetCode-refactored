## Increasing Triplet Subsequence

> Given an unsorted array return whether an increasing subsequence of length 3 exists or not in the array.
>
> Formally the function should return `true` if there exists `i,j,k` such that `arr[i] < arr[j] < arr[k]` where `0 ≤ i < j < k ≤ n - 1`, otherwise it should return `false`. 
>
> Note: Your algorithm should run in O(n) time complexity and O(1) space complexity.



### Strategy

What makes this question especially tricky is the linear time and constant space complexity requirements. Naturally, there's a catch. Or a trick, depends on whether you are the interviewee or the interviewer.

We need to go over the input array, and track two values:

1. The absolute min value, i.e., the lowest value we have seen in the array

2. Another value, the property of which is that we know for sure there's a lesser value on the left of it. 

   If we try to formalise it then given an input array `arr`, it's a value `B = arr[j]` such that there exists a value `A = arr[i]` for which `i < j` and `A < B`.


Essentially, it's the second value that gives us the edge here, since once we find a value that's greater than that value we win. Why? Because by definition there's a value that is lesser than the second value somewhere on it's left (see #2 above), and now we also found a value that is greater by scanning right, i.e., we are now is a position to declare there is an increasing triplet sequence.



### Code

As copied from LeetCode's discussion section (Java top voted, I've change some styling but otherwise it's a good old copy paste):

```java
public boolean increasingTriplet(int[] nums) {
  int small = Integer.MAX_VALUE, big = Integer.MAX_VALUE;

  for (int n : nums) {
    if (n <= small) { 
      small = n; 
    } else if (n <= big) { 
      big = n; 
    } else {
      return true; 
  }

  return false;
}
```



### Refactored

This is indeed a very short and to the point code. Even so, I feel like it is still hard to figure out. In this particular case I'm not sure there's much to do other than to make an extra effort and have the variable names as clear as possible, and even more importantly, have them tell the story.

I will rename `small` into `absoluteMin` since, after all, t represents the absolute minimum value in the input array.

Also, since the variable name `big` doesn't really tell us much I will rename it into `hasLesserOnLeft`, since having a lesser value on the left is a very important property in the context of this question. In fact, once we find a value that is greater than `hasLesserOnLeft` - we win (see the strategy section above).

In addition, I prefer to address the case of `num == small` explicitly, in its own `if` branch. In the original solution this is not the case, and the min value is set to `num` even if `num` is not strictly lesser (the condition is `n <= small`). While this does not affect the correctness of the solution, I believe it raises some questions, the main one being  "why would we do that?". The best answer I could come up with was to save ourselves an `if` branch. In this particular case, I believe it hurts readability, so I'd rather go for the extra if branch.

Finally, I would prefer to have the last condition state `hasLesserOnLeft < num` instead of `num > hasLesserOnLeft`, in order to visually indicate that num is on the right of `hasLesserOnLeft`. It is really important so why not have it strike the eye?

Here is the revised code:

```java
public boolean increasingTriplet(int[] nums) {
  
  int absoluteMin = Integer.MAX_VALUE;
  int hasLesserOnLeft = Integer.MAX_VALUE;

  for (int num : nums) {
    if (num == absoluteMin) {
      // skip
    } else if (num < absoluteMin) {
      absoluteMin = num;
    } else if (num < hasLesserOnLeft) { // absoluteMin < num < hasLesserOnLeft
      hasLesserOnLeft = num;
    } else if (hasLesserOnLeft < num) { // absoluteMin < hasLesserOnLeft < num (bingo!)
      return true;
    }
  }
  return false;
}
```

