## Letter Combinations of a Phone Number

> Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent.
>
> A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.
>
> ![img](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)
>
> **Example:**
>
> ```
> Input: "23"
> Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
> ```
>
> **Note:**
>
> Although the above answer is in lexicographical order, your answer could be in any order you want.
>
> Solution signature: `List<String> letterCombinations(String digits)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)



### Strategy

Like many backtracking questions, even when we have an intuition for what we would like to do, expressing it in code can still be challenging.

In this particular question, we'd like go over the input digits and generate strings consisting of all the combinations of each digit's letters with the other digits' letters.

Backtracking is all about constructing a *valid* computation path *step by step*, until a stop condition is met. Then, the path is added to the solution, which is a list of all the valid computation paths we've constructed. The reasons this technique is called backtracking is because the computation paths are constructed in a manner where the last step that was taken after completing a given path is 'rolled backed' (backtracked) to also consider (all) the alternatives steps and the different (possibly valid) computation paths they yield.

Let's see how this can be expressed in code.



### Code

Here's a very up-voted solution from LeetCode:

```java
public List<String> letterCombinations(String digits) {
  LinkedList<String> ans = new LinkedList<String>();
  if(digits.isEmpty()) {
    return ans;
  }
  
  String[] mapping = 
    new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
  
  ans.add("");
  
  while(ans.peek().length()!=digits.length()){
    String remove = ans.remove();
    String map = mapping[digits.charAt(remove.length())-'0'];
    for(char c: map.toCharArray()){
      ans.addLast(remove+c);
    }
  }
  return ans;
}
```

The structure is great, and we can see a couple of nice tricks:

* The mapping between digits and their corresponding letters is made very elegant by utilizing the following two considerations:
  1.  Mappings where the keys are sequential integers, or integers which fall within a reasonably narrow range, can be implemented using arrays
  2.  Char arrays (i.e., `char[]`) can written as strings, so instead of having `new char[] = {'c', 'a', 't'}` you could have `"cat".toCharArray()` which is more typing friendly. Doing so does incur some memory penalties since storing a string is more demanding that holding an array of chars, but this seems like a fair tradeoff in our context.

The variable naming in the above code however, could be clearer:

* Assuming `ans` is a short for `answer`, I do not believe saving 3 characters is worth having a cryptic name

* `mapping` can potentially communicate much more if named something like `digitLetters`, e.g., `digitLettesOn[1]`

* Statements that involve cryptic identifiers tend to spiral out of control, e.g.:

  *  `String remove = ans.remove()` 

  *  `ans.addLast(remove+c)`

  which are not very informative, and make the code much harder understand. 
  
  Ideally, identifiers should make the task of understanding code easier, not harder.



### Refactored

In this refactored version we preserve the main structure of the code, but make some changes in order for the code to be more readable and easier to understand. The most significant change we make is making the (overloaded) `letterCombinations(..)` method recursive, instead of iterative. This is done for in order to demonstrate a code template which can be reused for other questions, a template which I believe is easier to understand when expressed recursively.

```java
public List<String> letterCombinations(String digits) {
  List<String> combos = new ArrayList<>();
  letterCombinations(digits.toCharArray(), 0, new StringBuilder(), combos);
  return combos;
}
```

```java
private void letterCombinations(char[] digits,
                                int startFrom,
                                StringBuilder current,
                                List<String> combos) {
  if (startFrom == digits.length) { // stop condition
    combos.add(current.toString()); // add path to list of valid path
    return;
  }
  char digit = digits[startFrom];
  for (char letter : lettersOn(digit)) { // consider all alternatives for current step
    int length = current.length();
    current.append(letter);
    letterCombinations(digits, startFrom + 1, current, combos); // explore current
    current.setLength(length); // roll back the last step so alternatives can be considered
  }
}
```

```java
private final String[] LETTERS_ON_DIGITS =
      new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};

private char[] lettersOn(char digit) {
  int digitIndex = digit - '0';
  return LETTERS_ON_DIGITS[digitIndex].toCharArray();
}
```



