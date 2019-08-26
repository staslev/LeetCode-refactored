## Palindrome Linked List

> Given a singly linked list, determine if it is a palindrome.
>
> **Example 1:**
>
> ```
> Input: 1->2
> Output: false
> ```
>
> **Example 2:**
>
> ```
> Input: 1->2->2->1
> Output: true
> ```
>
> Solution signature: `boolean isPalindrome(ListNode head)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/palindrome-linked-list/)



### Strategy

To check wether a sequence (I'm deliberately using the abstract term "sequence" here), we would like to traverse it from both start and end at the same time, while comparing the characters we encounter. With singly linked lists however, we cannot easily traverse from the end so this will have to be worked around.

Here's the plan:

1. Reverse the second half of the list so we can traverse it from the end
   1. Traverse the list once to determine its length
   2. Reverse from the middle point onward
      1. There are two cases to consider, odd vs. even length
2. Traverse both the first half, and the (reversed) second half of the list while comparing characters
   1. We traverse the reversed part until we reach its end. This saves us the trouble of separately considering the odd and even length cases. The "reversed" list includes all the nodes that need to be equal, we need not care how many of them there are.
3. Restore the list to its original shape, or not. Question doesn't say so.



### (Leet)Code

Here's a very up-voted solution on LeetCode titled "Java, easy to understand":

```java
public boolean isPalindrome(ListNode head) {
  ListNode fast = head, slow = head;
  while (fast != null && fast.next != null) {
    fast = fast.next.next;
    slow = slow.next;
  }
  if (fast != null) { // odd nodes: let right half smaller
    slow = slow.next;
  }
  slow = reverse(slow);
  fast = head;

  while (slow != null) {
    if (fast.val != slow.val) {
      return false;
    }
    fast = fast.next;
    slow = slow.next;
  }
  return true;
}

public ListNode reverse(ListNode head) {
  ListNode prev = null;
  while (head != null) {
    ListNode next = head.next;
    head.next = prev;
    prev = head;
    head = next;
  }
  return prev;
}
```

While it may not be apparent at first glance, this solution pretty much follows the steps of the strategy discussed earlier. The fact we cannot tell it is so is a good indicator that refactoring is in order.

(Since reversing a linked list is a separate question which got its own chapter, I'm not going to get look into it here.)



### (Clean)Code

Let's express the strategy we devised earlier in code.

Step 1, which deals with reversing from the middle is task that can be encapsulated into its own method. Determining the length and considering whether it's odd or even will be done inside method.

Step 2 will then have to deal with traversing both halves and comparing characters.

```java
public boolean isPalindrome(ListNode head) {
  // step 1
  ListNode reversed = reverseSecondHalf(head);

  // step 2
  for (ListNode start = head, end = reversed;
       end != null;
       start = start.next, end = end.next) {
    if (start.val != end.val) {
      return false;
    }
  }
  return true;
}
```

And now the helper methods:

```java
private ListNode reverseSecondHalf(ListNode head) {
  int length = lengthOf(head);
  ListNode middle = 
    length % 2 == 0 ? 
    advance(head, length / 2) : 
    advance(head, length / 2 + 1);

  return reverse(middle);
}
```

```java
private int lengthOf(ListNode node) {
  int count = 0;
  while (node != null) {
    node = node.next;
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

If we were really nice and wanted to restore the list to its original form before wrapping up we could do something like this:

```java
private void restoreList(ListNode firstHalf, ListNode reversedHalf) {
  ListNode firstHalfTail = lastElementOf(firstHalf);
  if (firstHalfTail != null) {
    firstHalfTail.next = reverse(reversedHalf);
  }
}

private ListNode lastElementOf(ListNode head) {
  while(head != null && head.next != null) {
    head = head.next;
  }
  return head;
}
```

