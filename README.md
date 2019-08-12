## LeetCode Refactored

Common whiteboard coding interview questions solved with software engineering principles in mind.

Copyright © 2019 [Stas Levin](https://www.linkedin.com/in/staslevin/).

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-blue.svg)](LICENSE) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

###  [Table Of Contents](content/README.md)

### [FAQ](FAQ.md)

### Whom can this project be useful for?

* People preparing for whiteboard coding interviews
* People who use whiteboard coding interviews to assess candidates
* Computer science instructors wishing to combine algorithms and software engineering

#### People preparing for whiteboard coding interviews

Existing solution sets, and [there're plenty of them on GitHub](https://lmgtfy.com/?q=leetcode+solutions+github), tend to focus purely on the algorithmic nature of the problem. Getting the algorithm right is definitely crucial, but far from being the only, or arguably even the most important aspect of being a competent software engineer.

In this project I present solutions which strongly advocate for a number of qualities I find missing in common coding solutions on the web:

- *Readable* code
  - Clear and meaningful naming, concise methods, reasonable control flow, separation of concerns, etc.
- Code that reflects one's *[mental model](https://en.wikipedia.org/wiki/Mental_model)* of the solution
  - The introduction of abstractions to contain complexity and reflect how one sees and thinks of the solution

The end result is clear(er) solutions, which are easier to understand, and therefore reproduce, during technical code interviews. 

Each question discussed, consists of 3 main secions:

1. **Strategy**

   The approach used to solve the question, considerations, caveats, alternatives, etc.

2. **Common solution (code)**

   A top voted solution copy-pasted (modulo styling) from LeetCode. 

   These solutions often present code that is very challenging to read and understand, which leaves much to be desired. These issues are then discussed in the "Refactored" section.

3. **Refactored solution (code)**

   A revised solution that is based on the principles mentioned above - readable code which reflects the mental model of the solution.

#### People who use whiteboard coding interviews to assess candidates

If you're on the interviewing side of the table, you may or may not be aware of the [shortcomings of whiteboard coding interviews](FAQ.md#why-do-you-keep-repeating-the-phrase-corporate-whiteboard-interview-tragedy). I hope this project clearly demonstrates a number of key aspects and qualities you may be overlooking merely by using whiteboard coding interviews to assess candidates' engineering skills. Hopefully, this will help putting these engineering qualities on the check list, as well as raise a discussion about whether whiteboard coding interviews are indeed the best way to assess software engineers.

It feels that the assumption that whiteboard interviews allow software companies to efficiently scale has become an axiom, and the status quo, embraced by many tech companies worldwide. Arguments like "Google uses whiteboard interviews; Google scaled well and is very successful; ergo, whiteboard interviews help software companies scale well and be successful" are just not how causality works.

I'm afraid I was not able to find any scientific evidence (e.g., studies, papers) supporting the effectiveness of whiteboard coding interviews compared to other methods. If you come across anything of that sort please do share!

#### Computer science instructors wishing to combine algorithms and software engineering

IMHO and my personal experience algorithms and software engineering are usually taught in different courses. More often than not, people specialise in either but not both (another factor leading to the *corporate whiteboard interview tragedy*).

In this project I make an attempt at combining the two to produce readable (code) solutions to algorithmic problems. Something's gotta give, rarely can we have a solution that is both optimal (e.g., performance wise) and compliant with software engineering best practices. My goal is to make reasonable tradeoffs in order to produce an end result which is acceptable by both software and algorithm engineers. 

Comments are welcome.

### Why did you bother writing all this stuff?

To make the [world a better place](https://www.youtube.com/watch?v=B8C5sjjhsso), obviously. 

Mainly by:

* Increasing the awareness for the benefits of readable code
  * In a perfect world this would lead to more code being readable
* Increasing the awareness for the shortcoming of whiteboard coding interviews
  * In a perfect world this would give rise to better technical interviewing techniques

See the [FAQ](FAQ.md#does-the-world-really-need-another-leetcode-solution-set) for more info.

### That's a lot of words. TLDR please?

> **Move Zeros**
>
> Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.
>
> Solution signature is: `void moveZeroes(int[] nums)`

A solution can be easily found on LeetCode's discussion section:
```java
public void moveZeroes(int[] nums) {
  int i = 0;
  for (int n : nums) {
    if (n != 0) {
      nums[i++] = n;
    }	         
  }
  while (i < nums.length) {
    nums[i++] = 0;
  }
}
```

It's clean indeed, but could use a little structure. In fact, if we slightly refactor it, this code is happy to tell us what's actually going on:

```java
public void moveZeroes(int[] nums) {  
  int nonZeroBoundary = moveNonZerosToStart(nums); // check
  fillZeros(nonZeroBoundary, nums); // mate
}
```

then it's a matter of implementing the building blocks: `moveNonZerosToStart` and `fillZeros`. Which is a lot easier now that we know how these building blocks are going to be used to get us where we want.

#### So why is the latter better than the former?

Mostly because when one reads the revised version of the `moveZeroes` method, the steps towards the solution become blatantly clear. Each step's logic can then be isolated and implemented independently. 

Looking at the original solution, this is hardly the case. Even though it's clean, the cognitive load is far from trivial given the loops and the conditional expression in one of them.

A nice way to quantify this cognitive load is a metric called [cyclomatic complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity). This metric can be computed in IntelliJ by running an inspection fittingly named *"over complex method"*. Running this inspection on the original LeetCode solutions produced complexity warnings time after time. Running it on the solutions present here did not.

Kind colleagues pointed out [studies](https://par.nsf.gov/servlets/purl/10090357) on program comprehension which indicate that poor source code lexicon (e.g., identifier naming, name-intent inconsistencies, etc.) has a negative effect on code readability. This implies that storing valuable information in a variable named `i` may not be the best idea when considering code readability.

### Dude, do you even know what TLDR means?

Damn.
