## Merge Intervals

> Given a collection of intervals, merge all overlapping intervals.
>
> **Example 1:**
>
> ```
> Input: [[1,3],[2,6],[8,10],[15,18]]
> Output: [[1,6],[8,10],[15,18]]
> Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
> ```
>
> **Example 2:**
>
> ```
> Input: [[1,4],[4,5]]
> Output: [[1,5]]
> Explanation: Intervals [1,4] and [4,5] are considered overlapping.
> ```
>
> Solution signature: `int[][] merge(int[][] intervals) `.
>
> [Try it on LeetCode.](https://leetcode.com/problems/merge-intervals/)



### Strategy

Things get much easier once we sort the intervals by their start. Once they are sorted this way, it's a matter of inspecting each adjacent pair of intervals, if the right one starts before the left one ends, i.e., checking for overlap. If an overlap is detected it wold look like so:

```
s1     e1
|------|
     |-----|
     s2   e2
```

or like so:

```
s1     e1
|------|
  |--|
  s2 e2
```

or like so:

```
s1     e1
|------|
   |---|
   s2  e2
```

Either way the merged interval would be `[s1, max(e1, e2)]`.



### Code

Here's a very up-voted solution from LeetCode:

```java
public int[][] merge(int[][] intervals) {
  if (intervals.length <= 1)
    return intervals;

  // Sort by ascending starting point
  Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[0], i2[0]));

  List<int[]> result = new ArrayList<>();
  int[] newInterval = intervals[0];
  result.add(newInterval);
  
  for (int[] interval : intervals) {
    if (interval[0] <= newInterval[1]) { // Overlapping intervals, move the end if needed
      newInterval[1] = Math.max(newInterval[1], interval[1]);
    } else { // Disjoint intervals, add the new interval to the list
      newInterval = interval;
      result.add(newInterval);
    }
  }

  return result.toArray(new int[result.size()][]);
}
```

The code is generally well structured and it uses Java 8 to elegantly pass the comparator argument to `Arrays.sort(..)` as a lambda expression. 

The merging procedure in this code employs some trickery which consists of two main points:

* The input (array) is used as storage (and is destroyed) to avoid allocating new objects

  It all begins with allocating an initial `newInterval` that points to the first interval, then this `newInterval` is groomed (i.e., extended in case of overlap) until a non overlapping intervals arrives. Then, `newInterval` is assigned to this new non overlapping interval, and the grooming repeats until the next non overlapping interval. This way we get a groom-add-rinse-and-repeat kind of process which can be seen as a memory optimization since it avoids allocating new interval instances (except the initial one) by reusing the input intervals as storage. This makes the required space be `O(1)` on account of destroying the input.

  It should be noted that this process is destructive to the input intervals since they are mutated. If the input was to remain intact, it would be necessary to create new objects.

* `newInterval` always points to the last non overlapping interval, and new intervals are merged into it

  This makes the flow somewhat non trivial, since one need to realize that the `newInterval` variable is a state shared between loop iterations, moreover, `newInterval` is used to mutate the non overlapping intervals after they have already been added, via reference. While not rocket science, this may not be very intuitive.



### Refactored

In the refactored version we discuss the trickery points mentioned above in more detail, and try to make changes which would help the code look clearer (thus alleviating the tricky part).



Java 8 introduced `Comparator.comparingInt(..)` which can be used to construct comparators and is a nice API to have at our disposal. Passing in a lambda is just as great, but I wanted to provide a variant since the `Comparator.comparingInt()` also provides secondary comparisons using `thenComparingInt(..)`. In this case we don't need any.

```java
public int[][] merge(int[][] intervals) {
  if (intervals == null || intervals.length <= 1) {
    return intervals;
  }

  Comparator<int[]> byStart = Comparator.comparingInt((int[] interval) -> interval[START]);
  Arrays.sort(intervals, byStart);

  List<int[]> nonOverlapping = mergeSorted(intervals);
  
  return nonOverlapping.toArray(new int[0][]);
}
```

`mergeSorted(..)` is straight forward interval merge. It keeps a stack of all the non overlapping intervals it has produced thus far, and each iteration inspects the new interval against the last non overlapping one it produced.

```java
private List<int[]> mergeSorted(int[][] intervals) {
  Stack<int[]> nonOverlapping = new Stack<>();
  nonOverlapping.add(intervals[0]);
  for (int[] interval : intervals) {
    int[] previous = nonOverlapping.pop();
    int[][] merged = mergeSorted(previous, interval);
    Arrays.asList(merged).forEach(nonOverlapping::push);
  }
  return nonOverlapping;
}
```

The overloaded `mergeSorted(left, right)` returns an array of intervals which contains a single element in case the two input intervals were merged, and two (input) intervals in case the input intervals were disjoint. This allows the client to not worry about what happened and just add whatever `mergeSorted(left, right)` has returned to final list of non overlapping intervals.

This is a dummification of the more sophisticated merging-and-adding procedure described in the original solution, and while it allocates new objects in a far more liberal manner, the process is also more straight forward to understand.

```java
private int[][] mergeSorted(int[] left, int[] right) {
  if (right[START] <= left[END]) {
    return new int[][] {{left[START], Math.max(left[END], right[END])}};
  } else {
    return new int[][] {left, right};
  }
}
```

One way to reduce object allocation would be to pass the non overlapping intervals list (stack) directly to `mergeSorted(left, right)` so it can add whatever it wants. We're giving away some separation of concerns here, since `mergeSorted(..)` will now be responsible for both merging the input intervals AND adding the result to the non overlapping list, as opposed to before, where it only did the merging.

```java
private void mergeSorted(int[] left, int[] right, Stack<int[]> nonOverlapping) {
  if (right[START] <= left[END]) {
    nonOverlapping.push(new int[] {left[START], Math.max(left[END], right[END])});
  } else {
    nonOverlapping.push(left);
    nonOverlapping.push(right);
  }
}
```

We can in fact avoid allocating new objects entirely on account of destroying the input, similarly to the original solution from LeetCode. All we have to do is change the internals of the `mergeSorted(..)` method like so:

```java
private void mergeSorted(int[] left, int[] right, Stack<int[]> nonOverlapping) {
  if (right[START] <= left[END]) {
    left[END] = Math.max(left[END], right[END]);
    nonOverlapping.push(left);
  } else {
    nonOverlapping.push(left);
    nonOverlapping.push(right);
  }
}
```

Finally, if we really want to go crazy, we can have a "configuration" tell us whether the input objects should be preserved:

```java
private void mergeSorted(int[] left,
                         int[] right,
                         Stack<int[]> nonOverlapping, 
                         boolean preserveInput) {
  if (right[START] <= left[END]) {
    int[] merged;
    if (preserveInput) {
      merged = new int[] {left[START], Math.max(left[END], right[END])};
    } else {
      left[END] = Math.max(left[END], right[END]);
      merged = left;
    }
    nonOverlapping.push(merged);
  } else {
    nonOverlapping.push(left);
    nonOverlapping.push(right);
  }
}
```

Making the final version of `mergeSorted(int[][] intervals)` look like so:

```java
private List<int[]> mergeSorted(int[][] intervals) {
  Stack<int[]> nonOverlapping = new Stack<>();
  nonOverlapping.add(intervals[0]);
  for (int[] interval : intervals) {
    int[] previous = nonOverlapping.pop();
    mergeSorted(previous, interval, nonOverlapping, true);
  }
  return nonOverlapping;
}
```

We started with a design that prioritized clarity and separation of concerns, since the code was modular we could easily introduce changes to the merging process and keep improving the performance characteristics until they were satisfactory.