## Intersection of Two Linked Lists

> Write a program to find the node at which the intersection of two singly linked lists begins.
>
> For example, the following two linked lists begin to intersect at node `c1`.
>
> <img src="https://assets.leetcode.com/uploads/2018/12/13/160_statement.png" width="300">
>
> **Notes:**
>
> - If the two linked lists have no intersection at all, return `null`.
> - The linked lists must retain their original structure after the function returns.
> - You may assume there are no cycles anywhere in the entire linked structure.
> - Your code should preferably run in O(n) time and use only O(1) memory.
>
> Solution signature: `ListNode getIntersectionNode(ListNode headA, ListNode headB)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/intersection-of-two-linked-lists/)



### Strategy

There are a number of ways to approach this.

One of them, with a time complexity of *O(n+m)* and space complexity of *O(1)* could be:

1. Align both lists
   1. Compute the length of the first list and second lists
   2. Advance the longer to a node from which the length would be the same as the shorter one
2. From that node, advance both pointers at the same time until either they are equal in which case we have found the intersection point, or either is `null` which means we have reached the end of one of the lists without encountering any intersection so there is none

Another, more sophisticated way avoids computing the lengths of the lists altogether. This solution is presented [here](https://leetcode.com/problems/intersection-of-two-linked-lists/discuss/49785/Java-solution-without-knowing-the-difference-in-len!) and one of the comments also has a great visual explanation of why this actually works. Time and space complexity however, are the same as before so I will discuss the first solution in the interest of keeping things simple.

### Code

Here's a very up-voted solution from LeetCode's discussion section:

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
  int lenA = length(headA), lenB = length(headB);
  // move headA and headB to the same start point
  while (lenA > lenB) {
    headA = headA.next;
    lenA--;
  }
  while (lenA < lenB) {
    headB = headB.next;
    lenB--;
  }
  // find the intersection until end
  while (headA != headB) {
    headA = headA.next;
    headB = headB.next;
  }
  return headA;
}

private int length(ListNode node) {
  int length = 0;
  while (node != null) {
    node = node.next;
    length++;
  }
  return length;
}
```

This is very neat and commented, which is a great start. Given its current structure, it is quite easy to introduce helper methods and thus make this code even more clearer and concise.

The first two `while` loops are there to achieve one, and only one goal: advance the pointers until both lists are of the same length. These two `while` loops are a great opportunity to introduce a helper method. The same argument applies to the third and final `while` loop, which also has a very well defined goal: to walk both lists at the same time until they meet / both reach the end (`null`).



### Refactored

The refactored version is therefore as follows:

```java
public ListNode getIntersectionNode1(ListNode headA, ListNode headB) {
  ListNode[] alignedLists = align(headA, headB); // step 1
  return walkUntilEqual(alignedLists[0], alignedLists[1]); // step 2
}
```

This code clearly reflects the two steps we devised earlier in the strategy section.

Now the helper methods:

```java
private ListNode[] align(ListNode headA, ListNode headB) {
  int lengthA = length(headA);
  int lengthB = length(headB);
  int distance = Math.abs(lengthB - lengthA);

  return lengthB > lengthA
    ? new ListNode[] {headA, advance(headB, distance)}
    : new ListNode[] {headB, advance(headA, distance)};
}
```

```java
private ListNode walkUntilEqual(ListNode first, ListNode second) {
  while (first != second) {
    first = advance(first, 1);
    second = advance(second, 1);
  }
  return first; // or second, since they equal at this point
}
```

and some more helper methods:

```java
private int length(ListNode node) {
  int count = 0;
  while (node != null) {
    node = advance(node, 1);
    count++;
  }
  return count;
}
```

```java
private ListNode advance(ListNode node, int n) {
  for (int i = 0; i < n; i++) {
    node = node.next;
  }
  return node;
}
```



