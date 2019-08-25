## Valid Palindrome

> Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.
>
> **Note:** For the purpose of this problem, we define empty string as valid palindrome.
>
> [Try it on LeetCode.](https://leetcode.com/problems/valid-palindrome/)



### Strategy

In order to check whether a given string is a palindrome we need to traverse the it from both ends towards the middle checking wither the characters match until the left index crosses the right one. This is one of those cases where the explanation is clearer using actual code:

```java
for (int start = 0, end = s.length() - 1; start < end; start++, end--) {
  if(s.charAt(start) != s.charAt(end)) return false;
}
```

For example, for the input string `abcba` we would like to inspect the index pairs `(0,3)` and `(1,2)` which correspond to the characters `(a,a)` and `(b,b)`. These character pairs match, which makes `abcba` a palindrome.

In the question above things are a little tricker as we have some additional restrictions:

* Only consider alphanumeric characters, e.g., `_aa` is considered a palindrome (where `_` is a space)
* Character comparison is case insensitive, e.g.., `aA` is considered a palindrome

To meet these requirements we will make the proper adjustments when traversing the input string.

Time complexity here is *O(n)* (where `n` is the length of the string) since we scan the input string once.

Complexity is *O(1)* since we only use fixed space, that is, a fixed number of extra variables.



### (Leet)Code

Here's a solution from LeetCode:

```java
public boolean isPalindrome(String s) {
  
  if (s.isEmpty()) {
    return true;
  }
  
  int head = 0, tail = s.length() - 1;
  char cHead, cTail;
  
  while(head <= tail) {
    cHead = s.charAt(head);
    cTail = s.charAt(tail);
    if (!Character.isLetterOrDigit(cHead)) {
      head++;
    } else if(!Character.isLetterOrDigit(cTail)) {
      tail--;
    } else {
      if (Character.toLowerCase(cHead) != Character.toLowerCase(cTail)) {
        return false;
      }
      head++;
      tail--;
    }
  }

  return true;
}
```

The body of the `while` loop is pretty lengthy. One way to make it a bit more modular, would be to separate the loop into the following concerns:

* Figuring out comparable index pairs (which are such that both point are alphanumeric characters)
* Comparing the corresponding characters (i.e., ignore case)

In its current state, these two concerns are interwinded, making it hard both to understand the code, and to make it more modular.



### Refactored

Let's untangle the two concerns mentioned above so that each can be handled separately. 

One way to go about this separation is to have a `Supplier<Integer[]>` which produces all the comparable pairs. Once we have it in place we can code the solution like so:

```java
public boolean isPalindrome(String s) {
  if (s.equals("") || s.length() == 1) {
    return true;
  }

  // a supplier that produces comparable index pairs
  final Supplier<Integer[]> indexPairs = new ComparableIndexPairs(s);

  for (Integer[] indexPair = indexPairs.get(); 
       indexPair != null; 
       indexPair = indexPairs.get()) {
    if (!caseInsensitiveMatch(indexPair, s)) {
      return false;
    }
  }
  
  return true;
}
```

This will give as a better separation between the code responsible for deciding what index pairs should be compared, and the comparison itself. 

Let's implement `ComparableIndexPairs`:

```java
static class ComparableIndexPairs implements Supplier<Integer[]> {

  private String str;
  private int start;
  private int end;

  public ComparableIndexPairs(String str) {
    this.str = str;
    start = 0;
    end = str.length() - 1;
  }
  
  @Override
  public Integer[] get() {
    while (start < end) {
      char left = str.charAt(start);
      char right = str.charAt(end);
      
      // this code looks much like the while loop in the LeetCode solution
      // except it does not do the comparison part
      if (!Character.isLetterOrDigit(left)) {
        start++;
      } else if (!Character.isLetterOrDigit(right)) {
        end--;
      } else {
        Integer[] indices = { start, end };
        start++;
        end--;
        return indices;
      }
    }
    return null;
  }
}
```

It is not very surprising that the `get()` method, which is responsible for producing an index pair which should be compared, looks very similar to the code in the `while` loop of the original solution from LeetCode. The original code needed to figure out what indices to compare as well, it's just that it was mixed with other code as well. What we did was move out the code that dealt with this concern to a separate place. It is often the case that the question of WHERE to put code, is no less important than WHAT code to produce.

This separation is a step in the right direction, but we're still kinda struggling on the elegancy front, with the `if`  inside the `for` loop. The good news is that this can be remedied, the bad news is that we will need to put in some work to get there, most of which is due to the way Java is.

`if` clauses living inside `for` loops can often be replaced with `Streams` in Java. If we could somehow transform our `Supplier<Integer[]>` into a stream we'd be able to rewrite the code in a more elegant manner.

* In Java 9, where we have `Stream#takeWhile()` it looks like so:
  
  ```java
  public boolean isPalindrome(String s) {
    if (s.equals("") || s.length() == 1) {
      return true;
    }
    
    return
      Stream.generate(new ComparableIndexPairs(s))
      .takeWhile(pair -> pair != null) // Java 9 and above
      .allMatch(indexPair -> caseInsensitiveMatch(indexPair, s));
  }
  ```
  where `caseInsensitiveMatch` is a simple helper method:
  ```java
  private boolean caseInsensitiveMatch(Integer[] pair, String str) {
    char fromLeft = str.charAt(pair[0]);
    char fromRight = str.charAt(pair[1]);
    return Character.toLowerCase(fromLeft) == Character.toLowerCase(fromRight);
  }
  ```
  
* In Java 8, we will have to put in some extra work since `takeWhile` is only present in Java 9 and above. Without it, `Stream.generate(new ComparableIndexPairs(s))` is a bit problematic since it's an infinite stream, and at some point it will start producing nothing but `null`s. Without `takeWhile` we have no built-in (easy) way to terminate the stream once that happens. Yes, we could implement `takeWhile`, but one has to draw the line somewhere. Instead, let's implement the `Stream<Integer[]>` "manually" just for kicks. To do that we will need an iterator instance, which we can then plugin in using the low level `StreamSupport` API:
  
  ```java
  StreamSupport.stream(
    Spliterators.spliteratorUnknownSize(
        myLovelyIterator,  // comparable index pair iterator
        0),
    false);
  ```

  So now we're left with implementing the ```myLovelyIterator``` part which we will do using the `Supplier<Integer[]>` we've already built to enjoy and promote code reuse in the world:

  ```java
  static class ComparableIndicesIterator implements Iterator<Integer[]> {
  
    private ComparableIndexPairs comparableIndexPairs;
  
    private Integer[] nextPair;
  
    public ComparableIndicesIterator(ComparableIndexPairs comparableIndexPairs) {
      this.comparableIndexPairs = comparableIndexPairs;
    }
  
    @Override
    public boolean hasNext() {
      return nextPair != null || (nextPair = produceNext()) != null;
    }
  
    @Override
    public Integer[] next() {
      Integer[] next = nextPair != null ? nextPair : produceNext();
      nextPair = null; // mark consumed
      return next;
    }
  
    private Integer[] produceNext() {
      // code reuse FTW
      return comparableIndexPairs.get();
    }
  }
  ```

  

The iterator code has a number of optimizations in place to make `hasNext()` and `next()` work efficiently and in harmony. Each time the iterator is asked whether it `hasNext()` it checks its one-sized cache. If the cache has an item to offer, `true` is returned, otherwise it tries to compute the next element and stores it in the cache. If a new element is successfully computed, `true` is returned, otherwise `false`. The `next()` method consumes the one-sized cache, or produces a new element using the supplier passed in to the iterator's ctor. Generally `hasNext()` is to be called prior to invoking `next()`, while in this case correctness is maintained even if it doesn't. 

The iterator class is a stretch, and I would not try to pull it off during an actual interview (see also the "Epilogue" section). That said, in term's of software (over)engineering I think it's a nice exercise demonstrating how streams and iterators interact, and serve the purpose of modularity.

Anyway, now that we have a comparable index pair iterator at our disposal:

```java
  private Stream<Integer[]> comparableIndexPairStream(String str) {
    return StreamSupport.stream(
      Spliterators.spliteratorUnknownSize(
        new ComparableIndicesIterator(new ComparableIndexPairs(str)), // <== our iterator
        0),
      false);
  }
```

and finally, a Java 8 alternative to the version which used `takeWhile` only available in Java 9+:

  ```java
  public boolean isPalindrome(String s) {
    if (s.equals("") || s.length() == 1) {
      return true;
    }
  
  return
      comparableIndexPairStream(s) // constructs Stream<Integer[]>
      .allMatch(indexPair -> caseInsensitiveMatch(indexPair, s));
  }
  ```

### Epilogue

While it was fun implementing all these variations of the solution, some of them may be over the top for an interview. I think that the `Supplier<Integer[]>` version with Java 9+ stream API is the best one. Trying to force it with Java 8 without and IDE would be somewhat adventurous IMHO.
