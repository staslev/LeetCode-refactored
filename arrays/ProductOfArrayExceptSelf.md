## Product of Array Except Self

> Given an array `nums` of *n* integers where *n* > 1,  return an array `output` such that `output[i]` is equal to the product of all the elements of `nums` except `nums[i]`.
>
> **Note:** Please solve it **without division** and in O(*n*).
>
> **Follow up:**
> Could you solve it with constant space complexity? (The output array **does not** count as extra space for the purpose of space complexity analysis.)
>
> [Try it on LeetCode.](https://leetcode.com/explore/interview/card/top-interview-questions-hard/116/array-and-strings/827/)



### Strategy

Once way to go would be to construct an array `a` such that `a[i]` equals to `nums[0] * nums[1] … * nums[i]` and an array `b` such that `b[i]` equals to `nums[n-1] * num[n-2] … nums[i]`. Then we get `output[i] = a[i-1] * b[i+1]`. The arrays `a` and `b` require *O(n)* space and can be constructed in *O(n)* time.

The follow up asks for a constant space (excluding the output array) complexity so we'll need to make some adjustments. To achieve this we can use the output array to construct `a` and construct `b[i]` on the run, without storing it. If we construct `b[i]` in an orderly fashion, `b[i+1]` can be computed efficiently, and we can discard `b[i]` once we have `b[i+1]`.

Once we have this plan in mind, the main challenge that remains is getting the array indices bookkeeping right.



### Code

Most of the solutions on LeetCode go straight to answering the follow up question and provide a constant space solution. I think it's a nice exercise to first produce a less tight version of the space complexity and then improve upon it.



### Refactored

```java
public int[] productExceptSelf(int[] nums) {
  int[] productFromLeftUntil = productFromLeft(nums);
  int[] productFromRightUntil = productFromRight(nums);
  return productExceptSelf(productFromLeftUntil, productFromRightUntil);
}
```

Following the strategy we've devised:

```java
private int[] productExceptSelf(int[] productFromLeftUntil, int[] productFromRightUntil) {
  int n = productFromLeftUntil.length;
  int[] productExceptSelf = new int[n];

  productExceptSelf[0] = productFromRightUntil[1];
  productExceptSelf[n - 1] = productFromLeftUntil[n - 2];

  for (int i = 1; i <= n - 2; i++) {
    productExceptSelf[i] = productFromLeftUntil[i - 1] * productFromRightUntil[i + 1];
  }

  return productExceptSelf;
}
```

##### Follow up question (make space complexity constant)

Now we wish to use constant space, excluding the output array (i.e., the result). That's pretty much saying "use the output array to store intermediate shit".

```java
public int[] productExceptSelf(int[] nums) {
  int[] product = productFromLeft(nums);
  simulateProductFromRight(nums, product); // overwrites the second argument
  return product;
}
```

where `simulateProductFromRight` "simulates" the way we used to construct the `b` array earlier, only this time it constructs `b[i]` on the fly, uses it to construct `b[i+1]` and discards `b[i]`.

```java
private void simulateProductFromRight(int[] nums, int[] productFromLeftUntil) {
  int n = nums.length;
  productFromLeftUntil[n - 1] = productFromLeftUntil[n - 2];
  int productFromRight = nums[n - 1];
  for (int i = n - 2; i > 0; i--) {
    productFromLeftUntil[i] = productFromLeftUntil[i - 1] * productFromRight;
    productFromRight = productFromRight * nums[i];
  }
  productFromLeftUntil[0] = productFromRight;
}
```

`simulateProductFromRight` could have an `int[]` return type instead of `void`. The reasons I went for `void` after all was to canvey the fact this method mutates an input argument, wheres if its return type had been `int[]` it might have given the illusion of a new array being created and returned.



### Back to (Leet)Code

The same idea can be found on LeetCode, where the most upvoted solution looks like so:

```java
public int[] productExceptSelf(int[] nums) {
  int n = nums.length;
  int[] res = new int[n];
  res[0] = 1;
  for (int i = 1; i < n; i++) {
    res[i] = res[i - 1] * nums[i - 1];
  }
  int right = 1;
  for (int i = n - 1; i >= 0; i--) {
    res[i] *= right;
    right *= nums[i];
  }
  return res;
}
```

The main differences are naming and the introduction of a few helper methods which abstract away some of the complexity in this code. The complexity doesn't just vanish, but is it compartmentalized in a way that makes it easier to see what's going on.