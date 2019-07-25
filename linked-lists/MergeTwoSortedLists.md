## Merge Two Sorted Lists

> Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.
>
> **Example:**
>
> ```
> Input: 1->2->4, 1->3->4
> Output: 1->1->2->3->4->4
> ```
>
> Solution signature: `ListNode mergeTwoLists(ListNode l1, ListNode l2)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/merge-two-sorted-lists/)



### Strategy

As many linked lists questions, there's a recursive and an iterative approach.



### Code

#### Recursive

This very up-voted solution from LeetCode employs a recursive approach:

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2){
  if(l1 == null) return l2;
  if(l2 == null) return l1;
  if(l1.val < l2.val){
    l1.next = mergeTwoLists(l1.next, l2);
    return l1;
  } else{
    l2.next = mergeTwoLists(l1, l2.next);
    return l2;
  }
}
```

This recursive version is very neat and very easy to follow, great stuff.

In fact, the refactoring we will perform in the next section is mostly about style.

#### Iterative

Another highly up-voted solution take an iterative approach:

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
  if (l1 == null && l2 == null) {
    return null;
  }
  if (l1 == null) {
    return l2;
  }
  if (l2 == null) {
    return l1;
  }
  
  ListNode result = new ListNode(0);
  ListNode prev = result;
  
  while (l1 != null && l2 != null) {
    if (l1.val <= l2.val) {
      prev.next = l1;
      l1 = l1.next;
    } else {
      prev.next = l2;
      l2 = l2.next;
    }
    prev = prev.next;
  }
  if (l1 != null) {
    prev.next = l1;
  }
  if (l2 != null) {
    prev.next = l2;
  }
  return result.next;
}
```

Other than merging a bunch of `if` clauses to make this more compact, this is also a nice solution with a solid logic. The numerous `if` cases do hurt though, and make things look much more complex than they really are.



### Refactored

#### Recursive

```java
public ListNode mergeTwoLists3(ListNode l1, ListNode l2) {
  if (l1 == null || l2 == null) {
    return l1 == null ? l2 : l1;
  }

  ListNode source;
  ListNode other;

  if (l1.val < l2.val) {
    source = l1;
    other = l2;
  } else {
    source = l2;
    other = l1;
  }

  source.next = mergeTwoLists3(source.next, other);
  
  return source;
}
```



#### Iterative

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
  ListNode dummyHeadOfMerged = new ListNode(0);
  ListNode tail = merged;
  
  while (l1 != null && l2 != null) {
    if (l1.val <= l2.val) {
      tail = append(tail, l1);
      l1 = l1.next;
    } else {
      tail = append(tail, l2);
      l2 = l2.next;
    }
  }

  ListNode remainder = l1 != null ? l1 : l2;
  append(tail, remainder);

  return dummyHeadOfMerged.next; // skip the first dummy node
}
```

where `append(..)` is simply:

```java
private ListNode append(ListNode node, ListNode another) {
  node.next = another;
  return node.next;
}
```

A caveat worth noting here is that at first it may be tempting to have it so:

```java
while (l1 != null && l2 != null) {
  if (l1.val < l2.val) {
    mergedTail = append(mergedTail, l1);
    l1 = l1.next;
  } else if (l2.val < l1.val) {
    mergedTail = append(mergedTail, l2);
    l2 = l2.next;
  } else { // bug alert
    mergedTail = append(mergedTail, l1);
    mergedTail = append(mergedTail, l2);
    l1 = l1.next;
    l2 = l2.next;
  }
}
```

However, this will not produce the desired result due to the `else` clause:

```java
mergedTail = append(mergedTail, l1);
mergedTail = append(mergedTail, l2);
l1 = l1.next;
l2 = l2.next;
```

after the fist call to `append(..)`, the tail of the merged list becomes `l1`. Then, the second call to `append(..)` changes this tail and assigns  `l1.next` to point to `l2`.  This messes up the subsequent assignment `l1 = l1.next` since at this point  `l1.next` already points to `l2`. What this assignment actually does is assign `l1` to point to `l2`, which is not what we wanted. This, in turn, prevents the `while` loop from being able to traverse the remaining nodes in `l1`.

If you really wish to move the two pointers as part of the same `while` iteration, it needs to be carefully orchestrated so as not to destruct the next pointers before they are recorded for the next iteration:

```java
mergedTail = append(mergedTail, l1);
// must update l1 for the next iteration before its is mutated by append
l1 = l1.next; 
mergedTail = append(mergedTail, l2);
l2 = l2.next;
```

