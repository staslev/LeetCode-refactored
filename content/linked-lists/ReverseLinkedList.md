## Reverse Linked List

> Reverse a singly linked list.
>
> **Example:**
>
> ```
> Input: 1->2->3->4->5->NULL
> Output: 5->4->3->2->1->NULL
> ```
>
> **Follow up:**
>
> A linked list can be reversed either iteratively or recursively. Could you implement both?
>
> Solution signature: `ListNode reverseList(ListNode head)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/reverse-linked-list/)



### Strategy

An *intuitive recursive solution* would be to reverse the tail of the input list, that is, the input list excluding the first element, and then attach the excluded element to the tail of the reversed list:

* Base case: `head == null || head.next == null` in which case there's nothing to be done, just return `head`.
* Inductive step: assuming we know how to reverse a list of length `n-1` how can we reverse a list of length `n`?
  * Reverse the tail of the input list by excluding the first element. The tail is of length `n-1` so the induction hypothesis holds
  * Attach the previously excluded `head` element to the tail of the reversed tail. We have now reversed the entire input list. 

An *iterative solution* I find easy to follow is to reverse a pair of nodes each time, until we reach the end. Begin with the reversing the apir `(null, head)` then `(head, head.next)`, …`(last_element, null)`. The last element of the original list will become the head of the reversed list.



### Code

One of the most up-voted solutions on LeetCode looks like so:

```java
public ListNode reverseList(ListNode head) {
  return reverseListInt(head, null);
}

private ListNode reverseListInt(ListNode head, ListNode newHead) {
  if (head == null){
    return newHead;
  }
  ListNode next = head.next;
  head.next = newHead;
  return reverseListInt(next, head);
}
```

When the recursion ends with a recursive call (to itself) its called [tail recursion](https://cs.stackexchange.com/questions/6230/what-is-tail-recursion). Tail recursions can be optimised (but not always are) to perform better then non tail recursion, so this is a nice property to have. This property often comes at a certain cost and the code is not as intuitive. Java for instance, [has not implemented tail recursion optimization](http://www.drdobbs.com/jvm/tail-call-optimization-and-java/240167044) so far, so if we use Java we pay the price without getting much in return.

If we use a non-tail recursion here we can get a more intuitive solution, which looks much like an induction hypothesis and has a clear inductive step. 



### Refactor

#### Recursive

We'll go with a non tail recursive solution as discussed in the strategy section:

```java
public ListNode reverseList(ListNode head) {
  // base case
  if(head == null || head.next == null) {
    return head;
  }
  ListNode tail = head.next;
  // by induction
  ListNode reversed = reverseList(head.next);
  // attach the excluded element, i.e., the head
  tail.next = head;
  head.next = null;
  return reversed;
}
```

#### Iterative

An important thing to note here, is that we need to store the "pointers" to the next nodes before we reverse the current ones, since the reverse operation is destructive. 

We essentially have a window of size 2 move along the list, where each time we reverse the first and second element in the scope of this window. When the second element in this window is `null` it means the window has slid along the entire list and we should return the first element of the window, which is the head of the reversed list.

```java
public ListNode reverseList(ListNode head) {
  ListNode curr = null;
  ListNode next = head;
  while (next != null) {
    // store pointers to next node 
    // before they become unreachable
    ListNode futureCurr = next;
    ListNode futureNext = next.next;
    reverse(curr, next);
    // prepare for the next iteration
    curr = futureCurr;
    next = futureNext;
  }
  return curr;
}
```

