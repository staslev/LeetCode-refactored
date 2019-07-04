## Intersection of Two Arrays II
> Given two arrays, write a function to compute their intersection.
>
> Solution signature: `int[] intersect(int[] nums1, int[] nums2) `
>
> [Try it on LeetCode.](https://leetcode.com/problems/intersection-of-two-arrays-ii/)



### Strategy

We can go about solving this in a number of ways.

1. We can sort both arrays and then have two runner indices start from the beginning for each sorted array, and stop once one of them has reached the end of the array it's going over. Each step, compare the elements pointed by both these indices:
  
   * If the elements pointed by these indices are equals, add them to the intersection array and advance both.
   * If one is greater than the other advance only the one pointing to the lesser element
   
   In terms of complexity, the sorting costs us *O(nlogn + mlogm)* time where *m* and *n* are the lengths of the input arrays and *O(n + m)* space, the scan and compare stage is *O(min(m,n))* and it's dominated by the sorting costs.

2. Another option would be to select one of the input arrays, and build a mapping which maps each of its (unique) members to the number of time it appears in the array. Then, go over the second input array, and each time we encounter a number present in the mapping having a positive count, decrease that mapping's value by 1 and add the number to the intersection array. 

   In total this will cost us *O(n+m)* time and *O(n)* space, where *n* is the size of the array we built the mapping for, and *m* is the size of the other input array. 

   It is also apparent that since we have the *O(n)* space component in the cost, it's beneficial to build the mapping for the shorter array among the two input arrays.

   This strategy seems like the better one since it gives us better time and space complexities, so we'll work on getting it translated to code.



### Code

The following solution is one of the most upvoted (Java) ones on LeetCode's discussion section:

```java
// solution 1 (seen online)
public int[] intersect(int[] nums1, int[] nums2) {
  
  HashMap<Integer, Integer> map = new HashMap<>();
  ArrayList<Integer> result = new ArrayList<Integer>();

  for (int i = 0; i < nums1.length; i++) {
    if (map.containsKey(nums1[i])) {
      map.put(nums1[i], map.get(nums1[i])+1);
    } else {
      map.put(nums1[i], 1);
    }
  }

  for (int i = 0; i < nums2.length; i++) {
    if(map.containsKey(nums2[i]) && map.get(nums2[i]) > 0) {
      result.add(nums2[i]);
      map.put(nums2[i], map.get(nums2[i])-1);
    }
  }

  int[] r = new int[result.size()];
  for (int i = 0; i < result.size(); i++) {
    r[i] = result.get(i);
  }

  return r;
}
```



### Refactored

A number of changes can be made to make things clearer, while keeping the algorithmic soundness intact:

 - Some code can be extracted to separate methods (single responsibility)
	 - The first and second loops seem like good candidates
 - Variable naming could be clearer 
	 - `map`, `result` and `r` could tell more about their role using names such as `numToCount`, `matched`, etc.
 - It's best when data structure types appearing on the left hand side of an assignment have the most upper class possible (Liskov substitution principle)
	 - For example `List<Integer> matched = new ArrayList<>()` over `ArrayList<Integer> matched = new ArrayList<>()`
 - It's possible to utilize some newer Java APIs which make certain things simpler
	 -  `map.getOrDefault()` allows one to get a default value when a key is not present in the queried map
	 - `list.stream().mapToInt(num -> num).toArray()` can save us from from writing boilerplate code to convert a list of integers into an array of ints

Applying these changes results in the following code:

```java
public int[] intersect(int[] nums1, int[] nums2) {  
  Map<Integer, Integer> numToCount = buildIndex(nums1);  
  List<Integer> matched = consumeMatching(nums2, numToCount);  
  return matched.stream().mapToInt(num -> num).toArray();  
}
```
The new structure reveals the 3 main steps we take to solve this question:

 1. Build a mapping of values to their frequencies (counts)
 3. Match the second array against that mapping
 4. Convert the matched items into an array

Onto the helper methods:

```java
private Map<Integer, Integer> buildIndex(int[] nums1) {  
  Map<Integer, Integer> numToCount = new HashMap<>();  
  for (int num : nums1) {  
    int count = numToCount.getOrDefault(num, 0) + 1;  
    numToCount.put(num, count);  
  }
  return numToCount;  
}  

private List<Integer> consumeMatching(
  int[] nums2, Map<Integer, Integer> index) {
  
  List<Integer> matched = new ArrayList<>();  
  for (int num : nums2) {
    if (index.containsKey(num) && index.get(num) > 0) {  
      matched.add(num);  
      index.put(num, index.get(num) - 1);  
    }
  }  
  return matched;  
}
```

In some cases, people argue that clarity comes at the cost of verbosity. 
While in *some cases* it may be so (and even then, clarity may be well worth it), the above two solutions are on par in terms of LOC.  However, with the latter we have a much clearer solution which we can have easier time reproducing, as well as make things easier on the reader.

Personally, when I read lines such as `numToCount.put(num, count)` it makes me think that the person who wrote this code had my (i.e., the reader's) wellbeing on their mind, and it makes me feel all warm inside.

