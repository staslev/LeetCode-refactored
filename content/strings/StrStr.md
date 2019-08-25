## StrStr

> Implement [strStr()](http://www.cplusplus.com/reference/cstring/strstr/).
>
> Return the index of the first occurrence of needle in haystack, or **-1** if needle is not part of haystack.
>
> **Clarification:**
>
> What should we return when `needle` is an empty string? This is a great question to ask during an interview.
>
> For the purpose of this problem, we will return 0 when `needle` is an empty string. This is consistent to C's [strstr()](http://www.cplusplus.com/reference/cstring/strstr/) and Java's [indexOf()](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf(java.lang.String)).
>
> Solution signature: `int strStr(String haystack, String needle)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/implement-strstr/)



### Strategy

We can use brute for here, and check whether `needle` matches from any of the indices in `haystack` except those for which `i + needle.length() <= haystack.length()` does not hold, since if this is the case the length of `needle` can't possible fit into what's left of `hasystack`.

Time complexity is *O(n m)* (where `n` is the length of `haystack` and `m` is the length of `needle`) due trying to matching `needle` from any index in `haystack`.

Space complexity is *O(1)* since we only use a fixed number of extra variables.

There is also KMP, which is stands for Knuth–Morris–Pratt, which has an *O(n + m)* time complexity, and *O(m)* space complexity. Evidently, three very smart people have had to collaborate to come up with KMP, so it's a slim chance you will, on the spot, during an interview.



### (Leet)Code

A very up voted solution on LeetCode looks like so:

```java
public int strStr(String haystack, String needle) {
  for (int i = 0; ; i++) {
    for (int j = 0; ; j++) {
      if (j == needle.length()) return i;
      if (i + j == haystack.length()) return -1;
      if (needle.charAt(j) != haystack.charAt(i + j)) break;
    }
  }
}
```

It is indeed very concise, but it consists of nested loop with multiple conditions inside them. Such a structure is often an indication that we might be able to simplify things by refactoring or separating concerns.



### Refactored

First we can restructure a bit so that the string matching is separate from the looping:

```java
public int strStr(String haystack, String needle) {
  if (needle.equals("")) {
    return 0;
  }
  
  for (int i = 0; i < haystack.length() && canMatch(needle, haystack, i); i++) {
    if (matchFrom(i, needle, haystack)) {
      return i;
    }
  }
  
  return -1;
}
```

The matching part is placed in a separate method:

```java
private boolean matchFrom(int start, String needle, String haystack) {
  int i = start;
  int j = 0;
  while (i < haystack.length() && j < needle.length()) {
    if (haystack.charAt(i) != needle.charAt(j)) {
      return false;
    }
    i++;
    j++;
  }
  return j == needle.length();
}
```

where `canMatch(..)` is a helper method the checks where the a match is theoretically possible from a given index:

```java
private boolean canMatch(String needle, String haystack, int from) {
  return from + needle.length() <= haystack.length();
}
```

#### Streams to the rescue

This question is a good candidate for using Java streams, which can make the code even clearer. 

The streams the solution would look like so:

```java
public int strStr(String haystack, String needle) {
  if (needle.equals("")) {
    return 0;
  }

  return IntStream.range(0, haystack.length())
    .filter(start -> matchFrom(start, needle, haystack))
    .findFirst()
    .orElse(-1);
}
```

The laziness of Java streams allows us to nicely keep the optimization of not processing indices which are known to be far too deep into `haystack`, such that, `i + needle.length() <= haystack.length()` does not hold.  This is achieved by using `takeWhile(..)` :

```java
private boolean matchFrom(int start, String needle, String haystack) {
  return
    IntStream.range(start, needle.length())
    .takeWhile(from -> canMatch(needle, haystack, from))
    .allMatch(i -> start + i < haystack.length()
              && haystack.charAt(start + i) == needle.charAt(i));
}
```

