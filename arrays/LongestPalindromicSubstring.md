##  Longest Palindromic Substring

> Given a string **s**, find the longest palindromic substring in **s**. You may assume that the maximum length of **s** is 1000.
>
> Solution signature: `String longestPalindrome(String str)`



### Strategy 

A brute force solution cold be to, for example, to go over all pairs of indices `(i, j)` and select the pair where  `str.substring(i, j)` is a palindrome and `j - i` is greatest (i.e., length of the palindrome). Since there are *O(n^2)* such pairs, and checking whether `str.substring(i, j)` is linear in the palindrome length we end up with *O(n^3)* which is not ideal.

Another brute force oriented option would be to choose the palindrome center, instead of its start and end indices. Once we have chosen an index to be the palindrome center we can try and expand it towards the array boundaries until we encounter a violation, or succeed in reaching the beginning and end of the entire array. This expansion process is *O(n)* and there are *n* options to choose the center from, which results in an *O(n^2)* time complexity.

Another thing to note is that palindromes can be even or odd. In case they're odd, there is a single character in the middle which appears only once. In case they're even, there's no center character which appears only once, or we can think of it as dual-center, where the center is the two middle characters. For example `aba` is odd with center in `b`; `abba` is even with a dual-center in `bb`. When trying to expand a palindrome for an index `i` we should consider both cases in order to not miss possible palindromes.



If you google this question, wikipedia has a linear solution which we will not cover here.



### Code

Here's the code from LeetCode:

```java
public class Solution {

  private int lo, maxLen;

  public String longestPalindrome(String s) {
    int len = s.length();
    if (len < 2)
      return s;

      for (int i = 0; i < len-1; i++) {
        //assume odd length, try to extend Palindrome as possible
        extendPalindrome(s, i, i);
        //assume even length.
        extendPalindrome(s, i, i+1);
      }
      return s.substring(lo, lo + maxLen);
  }

  private void extendPalindrome(String s, int j, int k) {
    while (j >= 0 && k < s.length() && s.charAt(j) == s.charAt(k)) {
      j--;
      k++;
    }
    if (maxLen < k - j - 1) {
      lo = j + 1;
      maxLen = k - j - 1;
    }
  }
}
```

This code lays the groundwork with the `extendPalindrome` methods, but it has a number of aspects we would like to revise:

* `lo` and `maxLen` bring about state to the party. This state makes it harder to identify the flow. While class variables are not evil per-se, but `extendPalindrome` could definitely do without
* The `if` block inside `extendPalindrome` could be clearer



### Refactored  

Going over the input string, for each index `i` the steps we wish to perform are: 

1. Try building a palindrome with a (single) center at `i`
2. Try building a palindrome with a (dual) center at `i,i+1`
3. If any of the above constructed palindromes is greater than the current max, update max

Let's have the code reflect these steps:

```java
public String longestPalindrome(String str) {
  if (str.isEmpty()) {
    return str;
  }

  String longest = str.substring(0, 1);

  for (int i = 0; i < str.length(); i++) {
    int[] palindrome1 = buildPalindromeFromCenter(str, i);
    int[] palindrome2 = buildPalindromeFromCenter(str, i, i + 1);
    int[] localMax = maxPalindrome(palindrome1, palindrome2);
    longest = longest.length() > length(localMax) ? longest : toPalindrome(localMax, str);
  }
  
  return longest;
}

```

`buildPalindromeFromCenter(String str, int palindromeCenterIndex)` is simply an overload of the dual-center case:

```java
private int[] buildPalindromeFromCenter(String str, int palindromeCenterIndex) {
  return buildPalindromeFromCenter(str, palindromeCenterIndex, palindromeCenterIndex);
}
```

Building a palindrome from a dual-center at `center1` and `center2` consists of repeatedly trying to extend the right boundary of the palindrome more to the right, and the left boundary of the palindrome more to the left. If the palindrome is violated we stop the expansion loop and return what we have.

```java
private int[] buildPalindromeFromCenter(String str, int center1, int center2) {
  int start = center1;
  int end = center2;

  if (start < 0 || end > str.length() - 1 || str.charAt(start) != str.charAt(end)) {
    return null;
  }

  while (start > -1 && end < str.length()) {
    if (str.charAt(start) == str.charAt(end)) {
      start--;
      end++;
    } else {
      break;
    }
  }

  return new int[] { start + 1, end - 1 };
}
```

This leaves us with the utility methods we use to make it easier to work with `int[]` , which hold the palindromes' start and end indices. Holding the start and end indices allows us to defer obtaining the actual palindrome string until we must. 

```java
private String toPalindrome(int[] range, String str) {
  return str.substring(range[0], range[1] + 1);
}

private int[] maxPalindrome(int[] range1, int[] range2) {
  return length(range2) > length(range1) ? range2 : range1;
}

private int length(int[] palindrome) {
  return palindrome != null ? palindrome[1] - palindrome[0] + 1 : 0;
}
```

