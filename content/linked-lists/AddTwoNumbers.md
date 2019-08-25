## Add Two Numbers

> You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
>
> You may assume the two numbers do not contain any leading zero, except the number 0 itself.
>
> **Example:**
>
> ```
> Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
> Output: 7 -> 0 -> 8
> Explanation: 342 + 465 = 807.
> ```
> Solution signature: `ListNode addTwoNumbers(ListNode l1, ListNode l2)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/add-two-numbers/)



### Strategy

While the general idea is quite straightforward, and it is to traverse both lists at the same time, while summing up the digits obtained from each list, the bookkeeping needs to be carefully carried out and there are a few edge case to consider. 

* We should keep going for as long as either list, or the curry have not been consumed (i.e., have values)
* In each iteration we sum 3 values:

  1. A value from the first list (`l1`)
  2. A value from the second list (`l2`)
  3. The curry, if present (resulted from a previous sum being greater than `10`)
* If any of the lists has ended, the value it contributes is `0`
* Then, we advance both lists and perform another iteration
* Advancing a list that has already reached the end does nothing

We use a dummy node technique here, to keep a reference to the head node, while appending new digits to the tail. When we're done we get rid of the dummy head by returning its successor.



### (Leet)Code

A very up voted solution from LeetCode looks like so:

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
  ListNode c1 = l1;
  ListNode c2 = l2;
  ListNode sentinel = new ListNode(0);
  ListNode d = sentinel;
  int sum = 0;
  while (c1 != null || c2 != null) {
    sum /= 10;
    if (c1 != null) {
      sum += c1.val;
      c1 = c1.next;
    }
    if (c2 != null) {
      sum += c2.val;
      c2 = c2.next;
    }
    d.next = new ListNode(sum % 10);
    d = d.next;
  }
  if (sum / 10 == 1)
    d.next = new ListNode(1);
  return sentinel.next;
}
```

* `c1`, `c2`, `d` may not be the best names for variables as they convey very little meaning
* There are multiple `if` clauses inside the main loop, which may indicate a certain level of complexity 

But perhaps the far more significant issue here, is that while this code generally follows the strategy described earlier, it's not very easily to see that it does due to its structure. 

* Each iteration checks all over again if either list has ended, these `if` conditions make it hard to follow the main flow

* The code has several concerns which are addressed in several places in the loop's body. For example, some statements deal with the concern of advancing the list node pointers, while some statements deal with the concern of tracking the sum. However, both concerns are interwinded:

  ```java
  sum /= 10; // track sum
  if (c1 != null) {
    sum += c1.val; // track sum
    c1 = c1.next; // advance pointer
  }
  if (c2 != null) {
    sum += c2.val; // track sum
    c2 = c2.next; // advance pointer
  }
  if (sum / 10 == 1) // groom result list 
    d.next = new ListNode(1);
  ```

  It would be great if we could have some cohesion, i.e., if the same concern would be addressed in the same place like so:

  ```java
  {
    // track sum
  }
  {
    // advance pointers
  }
  {
    // groom the result linked list
  }
  ```

  Personally I prefer to advance the pointers at end of the loop, but as long as correctness is maintained that's no biggy.

  

### Refactored

I find it easier to have a helper method that simply gets a number from a list, note we can always get a number from a list. If the list is not `null`, the value returned is that of the head node, and the list is `null` the value returned is `0`. This makes things simpler for the main `while` , which can safely consume digits from both lists even if one has ended (in which case `0` will be returned).

Another nice trick is to include the `curry` flag in the `while` condition. Otherwise we would need check it explicitly after the `while` is done to avoid missing a final `1` resulting from a curry in the previous iteration. Including the `curry` is safe because even when the lists are done, `getDigit(..)` returns `0` and things work as expected.

We also have a `next(..)` method to safely advance lists, even if one has already reached the end.

Using these helper methods it is now easier to understand the flow, and see how it reflects our mental model of the solution.

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
  ListNode dummyHead = new ListNode(0);
  ListNode tail = dummyHead;
  boolean curry = false;

  while (l1 != null || l2 != null || curry) {
    int sum = getDigit(l1) + getDigit(l2) + (curry ? 1 : 0);
    curry = sum > 9;
    int currentDigit = sum % 10;
    tail = append(tail, currentDigit);
    l1 = next(l1);
    l2 = next(l2);
  }

  return dummyHead.next;
}
```

The helper methods are as follows:

```java
private ListNode next(ListNode node) {
  return node == null ? node : node.next;
}

private int getDigit(ListNode node) {
  return node == null ? 0 : node.val;
}

private ListNode append(ListNode node, int val) {
  node.next = new ListNode(val);
  return node.next;
}
```

If we go back to the cohesion discussion, the structure now is quite tight and cohesive:

```java
while(l1 != null || l2 != null || curry) {
  // track sum
  int sum = getDigit(l1) + getDigit(l2) + (curry ? 1 : 0);
  curry = sum > 9;
  // groom the result linked list
  int currentDigit = sum % 10;
  tail = append(tail, currentDigit);
  // advance pointers
  l1 = next(l1);
  l2 = next(l2);
}
```

Cohesion helps keep track of things which can be otherwise tricky.