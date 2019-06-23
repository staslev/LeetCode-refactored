## Contains Duplicate

>Given an array of integers, find if the array contains any duplicates.
>Your function should return true if any value appears at least twice in the array, and it should return false if every element is distinct.
>
>Solution: `boolean containsDuplicate(int[] nums)`



### Strategy

There is a number of ways to approach this question, depending on what time and space characteristics we wish to have.

1. Use the `Set` built-in data structure, which rejects duplicates. If an attempt to add a duplicate item is made, the `Set#add()` method does not add that item to the collection and returns `false`. We can therefore use a `Set` to identify whether any duplicates exist in the input array by simply adding each element to a  `Set` instance. 
   * Java's `HashSet` implementation [offers constant time](https://docs.oracle.com/javase/7/docs/api/java/util/HashSet.html) for `add(item)` so this strategy would take linear space and time.
2. We can sort the input array first. This will place the duplicates next to each other as a result. If the input was `1,2,3,4,3` sorting will result in `1,2,3,3,4` where the duplicate `3` appear consecutively. The time complexity of sorting is *O(nlong)* while the space complexity is *O(n)*. 
3. We can use `IntStream#distinct()` and let `IntStream` to the heavy lifting and then see if the size of the distinct set is lesser then the original set, this would mean some element have duplicates.
4. We can use constant space and brute force scan the array and the for the uniqueness of each element.  That is, take the first element (i.e., index `0`) and scan the remaining right hand side of the array for duplicates. If a duplicate is found return `true`, otherwise repeat the same for the next index. The space complexity will be *O(1)* while the time complexity will be *O(n^2)*.

We can see that the time complexity ranges from *O(n)* to *O(n^2)* and the space complexity ranges from *O(1)* to *O(n)*.



### Code

Here's a *O(n)* time and *O(n)* space complexity as copied from LeetCode's discussion section:

```java
// solution version 1 (seen online)
// linear time & complexity
// Set + for-loop
public boolean containsDuplicate(int[] nums) {
	Set<Integer> set = new HashSet<>();
	for(int i : nums) {
		if(!set.add(i)) { // a duplicate will return false
			return true; 
		}
	}
	return false;
}
```

The sorting solution code looks like so, again, copied from LeetCode's discussion section:
```java
// solution version 2 (seen online)
// o(nlogn) time, o(n) space (see also "Elements of 
// Programming Interviews in Java: The Insider's Guide")
// Arryas.sort() + loop
public boolean containsDuplicate(int[] nums) {
	if (nums.length ==0 || nums.length == 1){
		return false;
	}
	Arrays.sort(nums);
	for (int i=0;i<nums.length-1;i++){
		if (nums[i] == nums[i+1]){
			return true;
		}
	}
	return false;
}
```



###Refactored

#### Solution version 1

Extracting the for-loop into a separate method and using Java streams for going over the indices gives us:

```java
// solution version 2, restructured.
// Array.sort() + streams
private boolean seekSortedDuplicates(int[] nums) {  
	return IntStream.range(0, nums.length - 1)
		  .anyMatch(i -> nums[i] == nums[i + 1]); 
}  

public boolean containsDuplicate(int[] nums) {
	Arrays.sort(nums); // check
	return seekSortedDuplicates(nums); // mate
}
```
A benefit of using Java streams here, is that the if-clause in the original code which was meant to perform basic validations, can now be relieved.
If `nums.length == 0` or `nums.length == 1` then `IntStream.range(0, nums.length - 1)` returns an empty stream, in which case `anyMatch(...)` returns `false`, no if-clauses required.

The latter version makes it very clear that the solution consists of two main steps: 

1. Sort the array
2. Seek for duplicates in an already sorted array

If you really insist, a check for a `null` input can be added in the beginning. Whether it should or should not be there is debatable. In the scope of an interview it might be worth asking if getting a `null` as an input is a plausible scenario.

#### solution version 2

Since `Set#add()` returns `false` for duplicates, all we need to make sure of is that invoking it on every element in the input array returns `true`.

```java
public boolean containsDuplicate(int[] nums) {
  Set<Integer> distinct = new HashSet<>();
  return Arrays.stream(nums).allMatch(distinct::add);
}
```

Note that `allMatch` may not beyond the first element that yields `false`, short circuit style.