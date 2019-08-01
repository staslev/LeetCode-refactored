## Count and Say

> The count-and-say sequence is the sequence of integers with the first five terms as following:
>
> ```
> 1.     1
> 2.     11
> 3.     21
> 4.     1211
> 5.     111221
> ```
>
> `1` is read off as `"one 1"` or `11`.
> `11` is read off as `"two 1s"` or `21`.
> `21` is read off as `"one 2`, then `one 1"` or `1211`.
>
> Given an integer *n* where 1 ≤ *n* ≤ 30, generate the *n*th term of the count-and-say sequence.
>
> Note: Each term of the sequence of integers will be represented as a string.
>
> Solution signature: `String countAndSay(int n)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/count-and-say/)



### Strategy

I find that one of the trickiest parts in this question is to properly understand the derivation rules, i.e., how does one derive the next element in the "Count and Say" sequence given a previous element? 

(Some more examples are provided [in this discussion thread)](https://leetcode.com/problems/count-and-say/discuss/15995/Examples-of-nth-sequence)

Once this is figured out, and we realize that the way to go is to traverse the previous element, and count the number of (consecutive) appearances of each digit. Then, the count is written to the output string, followed by the digit that was counted, rinse and repeat until the end of the element being traversed.



### Code

A solution on LeetCode looks like so:

```java
public String countAndSay(int n) {
  StringBuilder curr = new StringBuilder("1");
  StringBuilder prev;
  int count;
  char say;
  for (int i = 1; i < n; i++) {
    prev = curr;
    curr = new StringBuilder();
    count = 1;
    say = prev.charAt(0);

    for (int j = 1, len = prev.length(); j < len; j++) {
      if (prev.charAt(j) != say) {
        curr.append(count).append(say);
        count = 1;
        say = prev.charAt(j);
      } else {
        count++;
      }
    }
    curr.append(count).append(say);
  }
  return curr.toString();

}
```

We can see that the naming is helpful, though the presence of nested loops and if conditions indicates that we might need to tidy up the structure a bit.



### Refactored

Essentially, in order to return the `n`-th element in the sequence we need to start from the first one and derive the next element for `n-1` times. It would be nice if this concept could be directly reflected in the code:

```java
public String countAndSay(int n) {
  String element = "1";
  for (int i = 1; i < n; i++) {
    element = countAndSayString(element);
  }
  return element;
}
```

now we need to figure out how to actually "count and say" a string element. This is done by traversing the string, and counting the successive appearances of each digit we encounter.

```java
private String countAndSayString(String str) {
  StringBuilder countAndSay = new StringBuilder();

  int i = 0;
  while (i < str.length()) {
    char digit = str.charAt(i);
    int count = countDigitAt(i, str);
    countAndSay.append(count).append(digit);
    i = i + count;
  }
  
  return countAndSay.toString();
}
```

where `countDigitAt(..)` counts the number of times a digit at a given index successively appears:

```java
private int countDigitAt(int i, String str) {
  int count = 1;
  while (i + count < str.length() && str.charAt(i + count) == str.charAt(i)) {
    count++;
  }
  return count;
}
```

