## Odd Even Linked List

> Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.
>
> You should try to do it in place. The program should run in O(1) space complexity and O(nodes) time complexity.
>
> **Example 1:**
>
> ```
> Input: 1->2->3->4->5->NULL
> Output: 1->3->5->2->4->NULL
> ```
>
> **Example 2:**
>
> ```
> Input: 2->1->3->5->6->4->7->NULL
> Output: 2->3->6->7->1->5->4->NULL
> ```
>
> **Note:**
>
> - The relative order inside both the even and odd groups should remain as it was in the input.
> - The first node is considered odd, the second node even and so on ...
>
> Solution signature: `ListNode oddEvenList(ListNode head)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/odd-even-linked-list/)



### Strategy

It may be easier to first consider the base cases:

* When the input list is of size 0 or 1, just return it as is
* Otherwise, we'll track a number of pointers (references):
  * the tail of the even linked list
  * the tail of the odd linked list
  * the head of the even linked list (since we'll need to append it to the odd's tail node)
* We need to traverse the input linked list, each time appending a node to either the odd list's tail, or the even list's tail, until we reach the end of the input list
  * Since we know the list is of size 2 at least, we always have a non `null` tails for both odd and even lists to begin with
  * We'll traverse the list using the even list's tail, i.e., we'll use `evenTail.next` and `evenTail.next.next` to traverse the input list, until `evenTail` becomes `null` and no further traversing can be done
* Finally, we will attach the even list's head to the odd list's tail to that we have all the odds followed by all the evens



### Code

A very up voted solution from LeetCode looks like so:

```java
public ListNode oddEvenList(ListNode head) {
  if (head != null) {

    ListNode odd = head, even = head.next, evenHead = even;

    while (even != null && even.next != null) {
      odd.next = odd.next.next;
      even.next = even.next.next;
      odd = odd.next;
      even = even.next;
    }
    odd.next = evenHead;
  }
  return head;
}
```

This code does follows the strategy we described very closely. However:

* It would be great to get rid of the `if` clause that encloses most of the code. 

  * If there's a corner case we'd like to handle, it is pretty standard to do so at the beginning of the method so that the rest of the method can assume a non corner case. That is, this code: 

    ```java
    if(cornerCase) {
      handle();
      return;
    }
    // non corner case code
    ```

    is generally preferred over this code:

    ```java
    if(nonCornerCase) {
      
    }
    // corner case code
    ```

    

* It would be nice if we could avoid tricky assignments where `next` appears multiple times on both sides of the assignments (e.g., `even.next = even.next.next`). It's really easy to get lost in all the `next` pointers.

  * This can be done by having a helper method, so `even.next = even.next.next` can then become `append(even, even.next.next)` which seem like a step in the right direction

  * Moreover, by having `append(..)` return the new tail of the appended list, we can have one liners like so:

     `even = append(even, even.next.next)` 

    instead of multi-liners like so:

    ```java
    even.next = even.next.next; 
    even = even.next;
    ```

While these may seem like minor issues, addressing them has a significant effect on code readability as we demonstrate below.



### Refactored

Following the comments above, below is a version that addresses them:

```java
public ListNode oddEvenList(ListNode head) {
  if (head == null || head.next == null) {
    return head;
  }

  ListNode oddTail = head;
  ListNode evenHead = head.next;
  ListNode evenTail = evenHead;

  while (evenTail != null && evenTail.next != null) {
    oddTail = append(oddTail, evenTail.next);
    evenTail = append(evenTail, evenTail.next.next);
  }

  append(oddTail, evenHead);

  return head;
}

```

