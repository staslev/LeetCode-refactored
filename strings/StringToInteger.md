## String to Integer (atoi)

> Implement `atoi` which converts a string to an integer.
>
> The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.
>
> The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.
>
> If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.
>
> If no valid conversion could be performed, a zero value is returned.
>
> **Note:**
>
> - Only the space character `' '` is considered as whitespace character.
> - Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. If the numerical value is out of the range of representable values, INT_MAX (2^31 − 1) or INT_MIN (−2^31) is returned.
>
> Solution signature: `int myAtoi(String str)`



### Strategy

This is the kind of questions where it's relatively clear what we need to do, but actually getting the code right is far from trivial.

As the instructions say, we need to skip any whitespaces until we get to the first non whitespace character in the input string. Then we may or may not have a sign character, and then any consecutive sequence of digits is the number we should parse and store in an integer. Moreover, if this process results in an overflow, the code should return INT_MAX (2^31 − 1) or INT_MIN depending on the sign.

The complexity here is *O(n)* (where `n` is the length of the string) since we scan the input string once.

### Code

A neat (and very up-voted) solution on LeetCode looks like so

```java
public int myAtoi(String str) {
  int index = 0, sign = 1, total = 0;
  //1. Empty string
  if(str.length() == 0) return 0;

  //2. Remove Spaces
  while(str.charAt(index) == ' ' && index < str.length())
    index ++;

  //3. Handle signs
  if(str.charAt(index) == '+' || str.charAt(index) == '-'){
    sign = str.charAt(index) == '+' ? 1 : -1;
    index ++;
  }

  //4. Convert number and avoid overflow
  while(index < str.length()){
    int digit = str.charAt(index) - '0';
    if(digit < 0 || digit > 9) break;

    //check if total will be overflow after 10 times and add digit
    if(Integer.MAX_VALUE/10 < total || 
       Integer.MAX_VALUE/10 == total && 
       Integer.MAX_VALUE %10 < digit)
      return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;

    total = 10 * total + digit;
    index ++;
  }
  return total * sign;
}
```

The comments in the code are very helpful. In fact they are so helpful we can promote the relevant code blocks to separate methods.

### Refactored

```java
public int myAtoi(String str) {
  if (str.equals("")) {
    return 0;
  }
  int firstNonSpace = consumeWhitespaces(str);
  return firstNonSpace <= str.length() - 1 ? parseNum(firstNonSpace, str) : 0;
}
```

Figuring out where the whitespaces end is straight forward. The trick part is parsing the actual number, which we divide into 2 tasks, parsing the sign (if present), and parsing the actual number:

```java
private int parseNum(int start, String str) {
  Optional<Integer> maybeSign = parseSign(start, str);
  Integer sign = maybeSign.orElse(1);
  try {
    int numStart = maybeSign.isPresent() ? start + 1 : start;
    return parseSignlessNum(numStart, sign, str);
  } catch (ArithmeticException e) {
    return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
  }
}
```

The sign character may not may not be present so `parseSign(..)` looks like so:

```java
private Optional<Integer> parseSign(int index, String str) {
  if (str.charAt(index) == '-') {
    return Optional.of(-1);
  } else if (str.charAt(index) == '+') {
    return Optional.of(1);
  } else {
    return Optional.empty();
  }
}
```

Alternatively we could use the return type `Integer` , and have `null` represent the case where no value is present. However, `Optional` has the benefit of clearly communicating this without documenting this special case, it's a good way to avoid having `null` pop up in unexpected places. I'm also not bothering with saving bits here, and storing the sign in an `Integer`. Once we have consumed the sign character, we know that the next things should be a number (or an invalid scenario):

```java
private int parseSignlessNum(int start, int sign, String str) {
  int num = 0;
  for (int i = start;
       i < str.length() && Character.isDigit(str.charAt(i));
       i++) {
    num = insertOnRight(num, digitAt(i, str));
  }

  return num * sign;
}

```

and the arithmetic helpers:

```java
private int digitAt(int i, String str) {
  return str.charAt(i) - '0';
}

private int insertOnRight(int num, int digit) {
  return Math.addExact(Math.multiplyExact(num, 10), digit);
}
```

Note that I'm using `Math.xxxExact()` to have an exception thrown in case of an overflow.