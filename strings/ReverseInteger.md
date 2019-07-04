## Reverse String

> Given a 32-bit signed integer, reverse digits of an integer.
>
> **Note:**
> Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.
>
> Solution signature: `int reverse(int x)`
>
> [Try it on LeetCode](https://leetcode.com/problems/reverse-string/).



### Strategy

This question mostly about understanding some properties of integers and the decimal representation:

`num / 10` results in a number whose values is the value of `num` without its last digit.

`num % 10` is the last digit in `num`.

Also worth noting is that when we perform an arithmetic operation on `num` which results in an overflow, performing the opposite operation may or may not result in the original integer. However, if the result does NOT equal to the original value, overflow MUST have occurred.

For example, `Integer.MAX_VALUE * 2 + 5 = 3`, `(3 - 5) / 2 = -1` and  `Integer.MAX_VALUE != -1`. Sometimes it can overflow back, `Integer.MAX_VALUE + 1 = Integer.MIN_VALUE` and `Integer.MIN_VALUE - 1 = Integer.MAX_VALUE`. The point being is that if the reverse math gives a different result than the original number, overflow MUST have occurred.

Armed with these insights we now need to iterate over the input `num` and assemble the reversed integer.

Time and complexity are linear in `n` where `n` is the number of digits in the input integer.

### Code

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



### Refactored

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

