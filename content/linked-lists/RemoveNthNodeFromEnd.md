## Remove Nth Node From End of List

> Given a linked list, remove the *n*-th node from the end of list and return its head.
>
> **Example:**
>
> ```
> Given linked list: 1->2->3->4->5, and n = 2.
> 
> After removing the second node from the end, the linked list becomes 1->2->3->5.
> ```
>
> **Note:**
>
> Given *n* will always be valid.
>
> **Follow up:**
>
> Could you do this in one pass?
>
> Solution signature: `ListNode removeNthFromEnd(ListNode head, int n)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)



### Strategy

We can approach this in a couple ways. 

1. A somewhat brute force way would be:
   1. To reverse the list, now the node which used to be `n`-th from the start, is actually `n`-th from the start
   2. If `n == 1` skip the first element
   3. If `n != 1` advance to the `n-1`th element and delete the `n`th node
   4. Reverse back the list
2. A somewhat more sophisticated way, would be to avoid reversing the list by keeping two runner pointers.
   1. Have the first runner get to the `n`th position in the list (by traversing from the start)
   2. If this runner points to `null`, it means that the length of the list is `n`, in which case what we need to do is remove the first element
   3. If it's not `null`, we set another runner to point to the `head` of the list, and then move the newly created runner and the previous one at the same time, one step at a time, until the runner to the right (i.e., that started at the `n` position) gets to the last element in the list (i.e., the 1st element from the end). When it does, it means that the other runner is now at a position `n-1` from the end, since by moving them at the same time, step by step we have maintained a gap of `n` elements between the two runners the entire time
   4. With the left runner is at position `n-1` from the end, it's straight forward to use it in order to delete the `n` element from the end which is simply the next element after its current position

The second option is what is somewhat misleadingly referred to in the question as "one pass" in the follow up question.



### (Leet)Code

Here's the code from LeetCode:

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
  ListNode start = new ListNode(0);
  ListNode slow = start, fast = start;
  slow.next = head;

  //Move fast in front so that the gap between slow and fast becomes n
  for(int i=1; i<=n+1; i++)   {
    fast = fast.next;
  }
  //Move fast to the end, maintaining the gap
  while(fast != null) {
    slow = slow.next;
    fast = fast.next;
  }
  //Skip the desired node
  slow.next = slow.next.next;
  return start.next;
}
```

It's very neat and clean, and even commented. It would be great though, if these comments could be used to extract helper methods so that the main code could be simpler and more structured. In the next section we use very similar ideas, but make an extra effort to keep the structure simple by employing helper methods.



### (Clean)Code

Let's try to express in code the steps and considerations we discussed as part of the second alternative in the strategy section:

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
  final ListNode nthPlusOne = advance(head, n);

  if (nthPlusOne == null) {
    return head.next;
  } else {
    ListNode nthMinusOne = advanceBoth(head, nthPlusOne);
    nthMinusOne.next = nthMinusOne.next.next;
    return head;
  }
}
```

`advance` looks like so:

```java
private static ListNode advance(ListNode node, int count) {
  for (int i = 0; i < count; i++) {
    node = node.next;
  }
  return node;
}
```

`advanceBoth` looks like so:

```java
private ListNode advanceBoth(ListNode left, ListNode right) {
  // we don't want to let the right runner get passed the last element
  while (right.next != null) {
    left = left.next;
    right = right.next;
  }
  return left;
}
```

What I like about this version is that is reflects our strategy quite nicely, no dummy nodes.

#### A linear brute force

Now let's express the first alternative discussed in the strategy section:

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
  // step 1
  ListNode reversed = reverse(head);

  if (n == 1) { // step 2
    reversed = reversed.next;
  } else { // step 3
    ListNode nthMinusOne = advance(reversed, n - 2);
    nthMinusOne.next = nthMinusOne.next.next;
  }

  // step 4
  return reverse(reversed); // i.e., back to the original list
}
```

Interestingly enough, both solutions are linear in `n`, i.e. *O(n)* where `n` is the length of the linked list. It's just that the more sophisticated one performs two list traversals at most (first runner, second runner), while the less sophisticated solution may perform three list traversals (reverse, delete, reverse).



#### Caveats

* There is a corner case when `n == size of list`, in which case we should just return `head.next` instead of trying to find the `(n-1)th` node (which hardly exists).