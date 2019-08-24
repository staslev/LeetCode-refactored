## First Unique Character in a String

> Given a string, find the first non-repeating character in it and return its index. 
>
> If it doesn't exist, return -1.
>
> **Note:** You may assume the string contain only lowercase letters.
>
> Solution signature: `int firstUniqChar(String s)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/first-unique-character-in-a-string/)



### Strategy

There are two main steps we need to perform:

1. Scan the input string and count how many times each character appears.
2. Scan the array again to figure out which, if any, character is the first unique one by inspecting the counts we've built.

Time complexity is *O(n)* where `n` is the length of the input string.

Space complexity is *O(1)* since the count array is know to be bounded in light of the face the input string consists of lowercase letters (and there are only 26 distinct ones).



### Code

The most up voted solution on LeetCode looks like so:

```java
public int firstUniqChar(String s) {
  int[] freq = new int[26];
  for(int i = 0; i < s.length(); i ++)
    freq [s.charAt(i) - 'a'] ++;
  for(int i = 0; i < s.length(); i ++)
    if(freq [s.charAt(i) - 'a'] == 1)
      return i;
  return -1;
}
```

Neat. However, even if we assume this code does what the strategy above says, it's not entirely clear what parts of the code relate to each step. Since we have several relaively independant `for` loops, exracting methods can make things clearer.



### Refactored

The original solution uses an array to store the character counts. Since there are 26 letters in the American ABC and the questions states that:

> **Note:** You may assume the string contain only lowercase letters.

the array which holds the counts need not be of size larger than 26.

Using an array is an optimisation aimed at squeeing the last ounce of performance. This trick will no longer work if the string is allowed to include other characters (i.e., the lower case letter restriction is removed).

Anyhow, let's refactor the code so that the two steps we devised in the strategy are clearly reflected in the code:

```java
public int firstUniqeChar(String s) {
  int[] frequencies = buildIndex(s);
  return firstUnique(s, frequencies);
}
```

The `buildIndex` method is pretty straight forward, we traverse the string and keep track of how many times we've seen it so far.

```java
private int[] buildIndex(String s) {
  int[] freq = new int[26];
  for(int i = 0; i < s.length(); i ++)
    freq [s.charAt(i) - 'a'] ++;
  return freq;
}
```

Once we have the counts at our disposal, we need to scan the string again to see which is the first unique character. This can be done using he good-old-brute-fore-if-in-the-middle `for` loop:

```java
private int firstUnique(String s, int[] freq) {
  for(int i = 0; i < s.length(); i ++)
    if(freq [s.charAt(i) - 'a'] == 1) {
      return i;
    }    
  return -1;
}
```

Below is a slightly more general version of the solution (which may come at a performance cost, since arrays can indeed outperform maps). Instead of using an array, we'll use a `Map<Character, Integer>` which is going to be to map chars to the number of times we've seen them. Since it's a map data structure we are no longer limited to lowercase letters.

```java
public int firstUniqChar(String s) {
  Map<Character, Integer> seen = buildCharIndex(s);
  return firstUnique(s, seen);
}
```

```java
public Map<Character, Integer> buildIndex(String str) {
  Map<Character, Integer> seen = new HashMap<>();
  for(char c : str.toCharArray()) {
    seen.put(c, seen.getOrDefault(c, 0) + 1);
  }
  return seen;
}
```

```java
public int firstUnique(String str, Map<Character, Integer> seen) {
  for(int i=0; i < str.length(); i++) {
    if(seen.get(str.charAt(i)) == 1) {
      return i;
    }
  }
  return -1;
}
```



#### Further optimization

While we're at it, a further optimization can be made to avoid scanning the input string twice, worst case. The second scan takes place to determine the first character that has a frequency of 1 (i.e., unique). Similarly to the first optimization where we kept a mapping of letters to their frequency in the input string, we can do the same for first occurrences. We only need to keep the fist index (occurrence) of each distinct letter in the input string.

```java
public int firstUniqChar(String s) {
  int[][] info = buildStats(s);
  int[] frequencies = info[0];
  int[] firstOccurrences = info[1];
  return firstUnique(s, frequencies, firstOccurrences);
}
```

`buildStats(..)` computes the frequencies and first index for each encountered distinct letter in the input string:

```java
private int[][] buildStats(String s) {
  int[] freq = new int[26];
  int[] firstSeen = new int[26];

  Arrays.fill(firstSeen, -1);

  for (int i = 0; i < s.length(); i++) {
    int letterKey = letterKey(s.charAt(i));
    freq[letterKey]++;
    if (firstSeen[letterKey] == -1) {
      firstSeen[letterKey] = i;
    }
  }
  return new int[][] {freq, firstSeen};
}

```

Finding the first unique character now involves looking up letters in both the frequency and occurrences mappings:

```java
private int firstUnique(String s, int[] frequencies, int[] firstOccurrences) {
  int firstUnique = Integer.MAX_VALUE;
  for (char letter = 'a'; letter <= 'z'; letter++) {
    int letterKey = letterKey(letter);
    if (frequencies[letterKey] == 1) {
      firstUnique = Math.min(firstUnique, firstOccurrences[letterKey]);
    }
  }
  return firstUnique != Integer.MAX_VALUE ? firstUnique : -1;
}

```

```java
private int letterKey(char letter) {
  return letter - 'a';
}
```