## Longest Substring Without Repeating Characters

> Given a string, find the length of the **longest substring** without repeating characters.
>
> Solution signature: `int lengthOfLongestSubstring(String str)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/longest-substring-without-repeating-characters/)



### Strategy

We can apply a brute force approach, and try each character as the starting point for our longest substring without repeating characters, and select the longest one. This will result in scanning to the right of each index in the array, an *O(n^2)* complexity.

This brute force approach can in fact be optimized, so that we only scan the entire array once to obtain the final result. The optimization makes use reuses the work we have done to ensure the uniqueness of the characters until the point a duplicate if found. Attempting to start our next longest non repeating string from the previous starting point + 1 is a waste of all the characters we've already scanned. 

We maintain a logical window, so that `str.substring(window.start, window.end + 1)` is a substring of `str` without repeating characters. We start with both the `window.start` and `window.end` set to `0`, i.e., the beginning of the input string. The brute force approach would try to advance `window.end` by one each time and examine for uniqueness. If the corresponding substring is no longer unique, then we record the current's window length and set `window.start` to `window.start + 1` , and set `window.end` to the new `window.start` so that we can start the whole process again starting from the new `window.start`. 

The optimization does not set `window.start` to `window.start + 1` when a duplicate is encountered. Instead, it sets `window.start` to `str.indexOf(duplicateChar, window.start) + 1`, that is, the immediate index to the right of the duplicate character within the current window. We have already made sure that `[str.indexOf(duplicateChar, window.start) + 1, window.end` is unique, since `window.end + 1` is the first index to violate the uniqueness of the window `[window.start, window.end]`. Once a duplicate is encountered we set the window to 
`[str.indexOf(duplicateChar, window.start) + 1, window.end + 1` and resume our attempts to extend `window.end` further to the right. It's important to note that `window.end` only moves forward, thus the linear time complexity of this optimization.

One final bit we should address is the part where we decide if a given character violates the uniqueness of the current window. To efficiently do this, we maintain a mapping between the characters we've encountered and their indices. When we encounter a duplicate and handle the required window readjustments, we need make sure this mapping is properly updated.



### Code

The most upvoted Java solution on LeetCode looks like so:

```java
public int lengthOfLongestSubstring(String s) {
  if (s.length()==0) return 0;
  HashMap<Character, Integer> map = new HashMap<Character, Integer>();
  int max=0;
  for (int i=0, j=0; i<s.length(); ++i){
    if (map.containsKey(s.charAt(i))){
      j = Math.max(j,map.get(s.charAt(i))+1);
    }
    map.put(s.charAt(i),i);
    max = Math.max(max,i-j+1);
  }
  return max;
}
```



### Refactored

```java
public int lengthOfLongestSubstring(String str) {
  if (str == null || str.length() == 0) {
    return 0;
  }

  int longest = 1;
  int[] window = new int[] {0, 0};
  Map<Character, Integer> seen = new HashMap<>();
  seen.put(str.charAt(0), 0);

  while (stretchWindowRight(window, str, seen) < str.length() - 1) {
    longest = Math.max(longest, windowLength(window));
    adjustWindowStartForDuplicate(window, str, seen);
  }

  return Math.max(longest, windowLength(window));
}
```

I intentionally keep this main method comment free so it's easier to focus solely one the code, and see how it reflects the main steps described in the previously devised strategy:

1. Extend (stretch) `window.end` to the right until the end of the string is reached, or a duplicate violates uniqueness
2. If a duplicate was encountered, adjust `window.start` in a way that reuses previous work
3. Repeat (1) + (2)

In order to properly handle cases where the window was extended until the string's end, and the `while` loop's condition is not met, we perform one last `Math.max(longest, windowLength(window))` before returning the final answer.

Since we'll be dealing with `window.start` and `window.end` quite a lot, I like the idea of making the access to a window's start and end coordinates easy.

```java
// cosmetics, to allow window[START/END] instead of window[0/1]
private static final int START = 0;
private static final int END = 1;
```

```java
private int windowLength(int[] window) {
  // if a window is [i,j] its substring's length is 2
  return (window[END] - window[START]) + 1;
}
```

`stretchWindowRight` is responsible for extending the end of the window (to the right) until a duplicate character is encountered, or the end of the string is reached. `isUniqueAfterWindowStart` checks whether a given character has been observed at, or beyond the current `window.start` (occurrences strictly before `window.start` are irrelevant):

```java
private int stretchWindowRight(int[] window, String str, Map<Character, Integer> seen) {
  int extendedEnd = window[END] + 1;
  while (extendedEnd < str.length()
         && isUniqueAfterWindowStart(str.charAt(extendedEnd), window[START], seen)) {
    seen.put(str.charAt(extendedEnd), extendedEnd);
    extendedEnd++;
  }
  window[END] = extendedEnd - 1;
  return window[END];
}
```

```java
private boolean isUniqueAfterWindowStart(char aChar,
                                         int windowStart,
                                         Map<Character, Integer> seen) {
  return !seen.containsKey(aChar) || seen.get(aChar) < windowStart;
}
```

`adjustWindowStartForDuplicate` is responsible for making adjustments to the window's start once a duplicate is known to be present at index `window.end + 1`:

```java
private void adjustWindowStartForDuplicate(int[] window, 
                                           String str, 
                                           Map<Character, Integer> seen) {
  char duplicateChar = str.charAt(window[END] + 1);
  int duplicateCharIndex = seen.get(duplicateChar);
  seen.remove(duplicateChar);
  window[START] = duplicateCharIndex + 1;
}
```

