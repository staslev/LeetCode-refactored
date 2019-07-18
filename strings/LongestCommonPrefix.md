## Longest Common Prefix

> Write a function to find the longest common prefix string amongst an array of strings.
>
> If there is no common prefix, return an empty string `""`.
>
> Editor's note: if the input array is empty `""` should be returned.
>
> Solution signature: `String longestCommonPrefix(String[] strs) `
>
> [Try it on LeetCode.](https://leetcode.com/problems/longest-common-prefix/)



### Strategy

1. Since we're looking for the longest *prefix* we should start traversing the arrays simultaneously, from the beginning
2. We should keep going until we encounter a non common character, or until we hit the end of one of the strings



### Code

One of the most up voted solutions on LeetCode looks like so:

```java
public String longestCommonPrefix(String[] strs) {
  if(strs == null || strs.length == 0) {
    return "";
  }
  
  String pre = strs[0];
  int i = 1;
  while(i < strs.length){
    while(strs[i].indexOf(pre) != 0)
      pre = pre.substring(0,pre.length()-1);
    i++;
  }
  return pre;
}
```

This solution represents a different mental modal from the one described in the strategy section. It first assumes that the longest common prefix is the first string in the input array, and then adjusts this assumption iteratively by making sure it is indeed a common prefix.

Let look into its complexity analysis.

`[String#substring` is *O(n)*](https://stackoverflow.com/questions/4679746/time-complexity-of-javas-substring) when `n` is the length of the substring'd string. 

`String#indexOf` is *O(k\*n)* where `k` and `n` are the lengths of the string being searched for and the string being searched in.

For the sake of simplicity, let's assume all the strings in the input are of length `n`.

In the solution above, the most outer `while` is going to be executed `L` times, where `L` is the number of strings in the input array. The inner `while`s condition is *O(n^2)* worst case since it uses `String#indexOf`. The body of the inner `while` can be executed at most *n* times.

If we sum it all up: *O(L\*(n^2) + n)*, i.e.,  *O(L\*(n^2))*.

It would seem that this solution is neither simple nor optimal. Let's see if this can be turned around.



### Refactored

Since I believe the strategy devised earlier (in the Strategy section) represents a simple mental model of the solution, rather than refactor the original solution, I will express this strategy in code:

```java
public String longestCommonPrefix(String[] strs) {
  if(strs == null) {
    return "";
  }

  // step 1
  int shortest = shortestLength(strs);

  // step 2
  int i = 0;
  while(i < shortest && isCommon(i, strs)) {
    i++;
  }
  
  return i == 0 ? "" : strs[0].substring(0, i);
}
```

now we're left with implementing the helper methods. `shortestLength` returns the length of the shortest string:

```java
private int shortestLength(String[] strs) {
  int length = Integer.MAX_VALUE;
  for(String str : strs) {
    length = Math.max(str.length(), length);
  }
  return length == Integer.MAX_VALUE ? 0 : length;
}
```

or, using Java streams:

```java
private int shortestLength(String[] strs) {
  return 
    Arrays.stream(strs)
    .mapToInt(String::length)
    .min()
    .orElse(0);
}
```

and `isCommon` checks whether the character at a given index is common to all the strings:

```java
private boolean isCommon(int index, String[] strs) {
  char charAtIndex = strs[0].charAt(index);
  for(int i = 1; i < strs.length; i++) {
    if(strs[i].charAt(index) != charAtIndex) {
      return false;
    }
  }
  return true;
}
```

or, using Java streams:

```java
private boolean isCommon(int index, String[] strs) {
  // assumes strs is NOT empty
  char charAtIndex = strs[0].charAt(index);
  return Arrays.stream(strs).allMatch(str -> str.charAt(index) == charAtIndex);
}
```

`shortestLength` and `isCommon` are linear in *L*, the length of the input string array. The single `while` loop traverses the shortest of the input strings, at most so we end up with *O(m \* L)* where *m* is the length of the shortest string in the array, and *L* is the number of string in the input array. Under the assumption that all strings are of length `n`, this would yield *O(n\*L)*, as opposed to the previous *O(L\*(n^2))*.

This is yet another good example of how an intuitive mental model of the solution helps produce a clear and performant solution.