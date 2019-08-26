## Reverse Integer

> Given a 32-bit signed integer, reverse digits of an integer.
>
> **Note:**
> Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.
>
> Solution signature: `int reverse(int x)`
>
> [Try it on LeetCode](https://leetcode.com/problems/reverse-integer/).



### Strategy

This question mostly about understanding some properties of integers and the decimal representation:

`num / 10` results in a number whose values is the value of `num` without its last digit.

`num % 10` is the last digit in `num`.

Another thing worth noting is the overflow thingy. There's always a ton of discussion around integer overflow, and how it can be detected. In the *general case* I'd go with one of the following:

* Using `long` and checking `Integer.MAX_VALUE`/`Integer.MIN_VALUE` (or narrowing down to `int` and checking the result)
* Using `Math.xxxExact(x, y)` which throws an `ArithmeticException` when things overflow

Armed with these insights we now need to iterate over the input `num` and assemble the reversed integer.

Time and complexity are linear in `n` where `n` is the number of digits in the input integer.



### (Leet)Code

One of the most upvoted solutions on LeetCode looks like so:

```java
public int reverse(int x) {
  int result = 0;

  while (x != 0) {
    int tail = x % 10;
    int newResult = result * 10 + tail;
    if ((newResult - tail) / 10 != result) { 
      return 0; 
    }
    result = newResult;
    x = x / 10;
  }
  
  return result;
}
```

In this solution overflow is detected using the following check:

```java
(newResult - tail) / 10 != result
```

It should be noted that in the general case, this may not be sufficient to determine whether an overflow has occurred. For example, if `result` is `214748364` and `tail` is `9` then `newResult = -2147483647`. Then,`(-2147483647 - 9)/10` is again `214748364`, i.e.,  `(newResult - tail) / 10 == result` in the present of an overflow. This would mean that the reversing the number `9463847412` would not return `0` as required. However, since the question states that the input is a 32-bit integer, `9463847412` falls outside the input range and need not be considered. The 32-bit restriction on the input makes the check `(newResult - tail) / 10 != result` sufficiently strong to detect overflows within the given input range.



### (Clean)Code

By simply tidying up, extracting some methods, and renaming some variables we get to this:

```java
public int reverse(int num) {
  Integer reversed;
  for (reversed = 0; num != 0 && reversed != null; num = shiftRight(num)) {
    reversed = insertOnRight(reversed, lastDigitOf(num));
  }
  return reversed == null ? 0 : reversed;
}
```

The `while` was changed to a `for` loop, which has the benefit of making the loop's "advance" expression clearer. We can now see it's `num = shiftRight(num)`.

The loop's body has also changed, and it is now a single, and just as important, simple line. We're inserting `lastDigitOf(num)` to the right of `reversed`. If `insertOnRight(…)` returns `null`, it means that an overflow has occurred and we should break. This is why the (`for`'s) termination condition includes a `reversed != null`.

```java
private Integer insertOnRight(int num, int digit) {
  int withDigitOnRight = shiftLeft(num) + digit;
  boolean isOverflow = (withDigitOnRight - digit) / 10 != num;
  return isOverflow ? null : withDigitOnRight;
}
```

and the remaining utility methods:

```java
private int shiftLeft(int num) {
  return num * 10;
}

private int shiftRight(int num) {
  return num / 10;
}

private int lastDigitOf(int num) {
  return num % 10;
}
```

