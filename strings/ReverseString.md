## Reverse String

> Write a function that reverses a string. The input string is given as an array of characters `char[]`.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array in-place** with O(1) extra memory.
>
> You may assume all the characters consist of [printable ascii characters](https://en.wikipedia.org/wiki/ASCII#Printable_characters).
>
> Solution signature: `void reverseString(char[] s)`
>
> [Try it on LeetCode.](https://leetcode.com/explore/interview/card/top-interview-questions-easy/127/strings/879/)



### Strategy

A relatively straight forward solution would be to traverse the string (or array) from both ends, start and end, at the same time, while swapping the characters.

Time complexity is *O(n)* since we scan the array one.

Space complexity is *O(1)* since we perform the reversing in place (with a few extra helper variables).

### Code

Consider the following code (copy-pasted and adjusted from LeetCode's discussion section):

```java
public void reverseString(char[] s) {
  int i = 0;
  int j = s.length() - 1;
  while (i < j) {
    char temp = s[i];
    word[i] = s[j];
    s[j] = temp;
    i++;
    j--;
  }
}
```



### Refactored

The code above is quite neat and concise. Perhaps somewhat surprisingly, we can still tidy it up a bit:

```java
public void reverseString(char[] s) {
  for (int start = 0, end = s.length - 1; start < end; start++, end--) {
    swap(start, end, s);
  }
}
```

where `swap` does the heavy lifting:

```java
private void swap(char[] arr, int i, int j) {
  char temp = arr[i];
  arr[i] = arr[j];
  arr[j] = temp;
}
```

What I like about the refactored version is that the body of the `for` loop is really simple, and the bookkeeping of the indices is done in the `for` itself, where it belongs. That is in contrast to the original code, where the bookkeeping is performed *both* prior to the `while` loop, and inside the `while` loop.

Believe it or not, both version are the same number of lines.