## Valid Anagram

> Given two strings *s* and *t* , write a function to determine if *t* is an anagram of *s*.
>
> Solution signature: `boolean isAnagram(String s, String t)`
>
> [Try it on LeetCode.](https://leetcode.com/explore/interview/card/top-interview-questions-easy/127/strings/882)
>
> **Note:**
> You may assume the string contains only lowercase alphabets.
>
> **Follow up:**
> What if the inputs contain unicode characters? How would you adapt your solution to such case?



### Strategy

1. Scan the first (or second) input string and count how many times each character appears/
2. Scan other input string and update the char counts of the previously built counts
3. Check whether the counts are all `0`, i.e., whether all chars in `s` were matched by chars in `t`



### Code

A very upvoted solution on LeetCode looks like so:

```java
public boolean isAnagram(String s, String t) {
  int[] alphabet = new int[26];
  for (int i = 0; i < s.length(); i++) alphabet[s.charAt(i) - 'a']++;
  for (int i = 0; i < t.length(); i++) alphabet[t.charAt(i) - 'a']--;
  for (int i : alphabet) if (i != 0) return false;
  return true;
}
```

As it is the case with many solutions on LeetCode, while compact and neat, it's hard to see what's going on even after we are familiar with the solution strategy.

### Refactored

My goal here is to keep the `isAnagram()` method as clear as possible, abstracting away according to the strategy:

```java
public boolean isAnagram(String s, String t) {
  int[] sIndex = buildCharCounts(s); // step 1
  consumeCharCounts(sIndex, t); // step 2
  return isAllZeros(sIndex); // step 3
}
```

then it is a matter of implementing these building blocks:

```java
private int[] buildCharCounts(String str) {
  int[] charIndex = new int[26];
  for(char c : str.toCharArray()) {
    charIndex[keyOf(c)]++;
  }
  return charIndex;
}

private void consumeCharCounts(int[] index, String str) {
  for(char c : str.toCharArray()) {
    index[keyOf(c)]--;
  }
}
```

finally, we're left with the utility methods:

```java
private int keyOf(char c) {
  return c - 'a';
}

private boolean isAllZeros(int[] index) {
  for(int count : index) {
    if(count != 0) {
      return false;
    }
  }
  return true;
}
```

