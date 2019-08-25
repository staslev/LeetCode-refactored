## Count Primes

> Count the number of prime numbers less than a non-negative number, **n**.
>
> **Example:**
>
> ```
> Input: 10
> Output: 4
> Explanation: There are 4 prime numbers less than 10, they are 2, 3, 5, 7.
> ```
>
> Solution signature: `int countPrimes(int n)`
>
> [Try it on LeetCode.](https://leetcode.com/problems/count-primes/)



### Strategy

A brute force approach would be to test each number `k`  in the range (1, n], by trying to divide it by all the numbers in the range (1, k). If any such number is found `k` is not prime, otherwise, it is. Then we can sum all the prime ones. While correct, this is highly inefficient.

A more efficient way is called the [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes), which would be a challenging to invent on the spot is it's better to just familiarize yourself with it beforehand. There are a number of nice illustrations on the web, depicting how it actually works.



### (Leet)Code

The following from LeetCode implements the Sieve of Eratosthenes:

```java
public int countPrimes(int n) {
  if(n <=1 ) return 0;

  boolean[] notPrime = new boolean[n];        
  notPrime[0] = true; 
  notPrime[1] = true; 

  for(int i = 2; i < Math.sqrt(n); i++){
    if(!notPrime[i]){
      for(int j = 2; j*i < n; j++){
        notPrime[i*j] = true; 
      }
    }
  }

  int count = 0; 
  for(int i = 2; i< notPrime.length; i++){
    if(!notPrime[i]) count++;
  }
  return count; 
}
```

The wikipedia page [suggests the following steps](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes#Overview) to "find all the prime numbers less than or equal to a given integer `n` by Eratosthenes' method":

> 1. Create a list of consecutive integers from `2` through `n`: `(2, 3, 4, ..., n)`.
>    Initially, let `p` equal `2`, the smallest prime number.
> 2. Enumerate the multiples of `p` by counting in increments of `p` from `2p` to `n`, and mark them in the list (these will be `2p, 3p, 4p ...`; the `p` itself should not be marked).
> 3. Find the first number greater than p in the list that is not marked. If there was no such number, stop. Otherwise, let `p` now equal this new number (which is the next prime), and repeat from step 3.
> 4. When the algorithm terminates, the numbers remaining not marked in the list are all the primes below `n`.



### Refactored

It would be great if we could clearly reflect the steps above in code so it's aligned with the mental model of the solution.

```java
private final static int FIRST_PRIME = 2;

public int countPrimes(int n) {
  if (n <= 1) {
    return 0;
  }
  
  boolean[] primeCandidates = new boolean[n];
  // step 1
  Arrays.fill(primeCandidates, FIRST_PRIME, n, true);

  double sqrt_n = Math.sqrt(n);
  // step 2
  for (int prime = FIRST_PRIME;
       prime < sqrt_n; 
       prime = nextTrueAfter(prime, primeCandidates) /* step 3 */) {
    ruleOutMultiplesOf(prime, primeCandidates); // also part of step 2
  }
  // step 4
  return countTrue(primeCandidates);
}
```

And now the helper methods, which are essentially loops extracted to separate methods so they can be clearly named to communicate their intent:

```java
private void ruleOutMultiplesOf(int prime, boolean[] primes) {
  for (int multiplier = prime; multiplier * prime < primes.length; multiplier++) {
    primes[prime * multiplier] = false;
  }
}
```

```java
private int nextTrueAfter(int index, boolean[] booleans) {
  int candidate = index + 1;
  while (candidate < booleans.length && !booleans[candidate]) {
    candidate++;
  }
  return candidate;
}
```

```java
private int countTrue(boolean[] primeCandidates) {
  int count = 0;
  for (int i = FIRST_PRIME; i < primeCandidates.length; i++) {
    if (primeCandidates[i]) {
      count++;
    }
  }
  return count;
}
```

