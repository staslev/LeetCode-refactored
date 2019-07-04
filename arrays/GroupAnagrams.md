## Group Anagrams

> Given an array of strings, group anagrams together.
>
> Solution signature: `List<List<String>> groupAnagrams(String[] strs)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/group-anagrams/)



### Strategy

If we could come up with function that returned the same key for all anagrams, this function along with a  map data structure could be used to put all anagrams under the same key. Sorting for example, can be such a function, because all anagrams have the same set of characters and when sorted, will produce the same key.



### Code

Here's one of the most upvoted solutions on LeetCode:

```java
public List<List<String>> groupAnagrams(String[] strs) {
  if (strs == null || strs.length == 0) return new ArrayList<List<String>>();
  Map<String, List<String>> map = new HashMap<String, List<String>>();
  for (String s : strs) {
    char[] ca = s.toCharArray();
    Arrays.sort(ca);
    String keyStr = String.valueOf(ca);
    if (!map.containsKey(keyStr)) map.put(keyStr, new ArrayList<String>());
    map.get(keyStr).add(s);
  }
  return new ArrayList<List<String>>(map.values());
}
```

Perhaps somewhat surprisingly, while this is short and to the point we can still make things clearer and easier to read.



### Refactored

All the essential code is already there, we just wish to restructure it so it clearly reflects our strategy which is:

1. Get the key of the given string
2. Assign the given string under its key in a map data structure
3. Return all the values in the map

Let's translate that to code:

```java
public List<List<String>> groupAnagrams(String[] strs) {
  Map<String, List<String>> anagrams = new HashMap<>();
  
  for (String str : strs) {
    String key = keyOf(str);
    add(anagrams, str, key);
  }
  
  return new ArrayList<>(anagrams.values());
}
```

and the helper methods:

```java
private String keyOf(String str) {
  char[] chars = str.toCharArray();
  Arrays.sort(chars);
  return new String(chars);
}

private void add(Map<String, List<String>> anagrams, String str, String key) {
  List<String> group = anagrams.getOrDefault(key, new ArrayList<>());
  group.add(str);
  anagrams.put(key, group);
}
```

We could also employ Java streams, where a grouping collector does the heavy lifting for us:

```java
public List<List<String>> groupAnagrams(String[] strs) {
  Map<String, List<String>> keyedAnagrams =
    Arrays.stream(strs)
    .collect(Collectors.groupingBy(this::keyOf));
  return new ArrayList<>(keyedAnagrams.values());
}
```