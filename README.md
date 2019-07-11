## LeetCode Refactored

Refactored solutions for assorted LeetCode questions.

Copyright © 2019 [Stas Levin](https://www.linkedin.com/in/staslevin/).

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-blue.svg)](LICENSE) [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

###  [Table Of Contents](TOC.md)

###  [FAQ](FAQ.md)

### Does the world really need another LeetCode solution set?

Probably not. Who knows what the world really needs these days.

What I have noticed though, is that existing solution sets, and [there're plenty of them on GitHub](https://lmgtfy.com/?q=leetcode+solutions+github), tend to focus purely on the algorithmic nature of the problem. Consequently, I believe many of them leave much to be desired as far as *software engineering* goes, readability (and therefore also maintainability) in particular.

It could actually be funny, if it wasn't  so tragic, because I assume most of the people who wind up on [LeetCode](https://leetcode.com/) probably do so in order to prepare for an interview for a software engineering position, in the scope of which they will be practicing software engineering. Corporate whiteboard interviewers tend to put little emphasis on checking for software engineering qualities in favour of whiteboard skills, resulting in a process optimised for the survival of whiteboard engineers.

It never ceases to amaze me, how often, and to what extent, common solutions present code that would fail to pass a typical code review. Now, I'm by no means saying that these solutions aren't clever, or even occasionally genius, what I'm saying is that things like code structure & readability seem to be the first casualties in the *corporate whiteboard interview tragedy*.

Personally I happen to believe in software engineering practices, and wish more of them could be taken into account when educating and interviewing software engineers. To that end, my goal with this project it to try and present a different angle on LeetCode questions, one which incorporates some of my beliefs and perspectives, gathered over the past decade and a half of professionally practicing (in the industry) and researching (in academia) software engineering. 

In particular I advocate for:
 - A very *low tolerance for unreadable* code
 - Code that reflects an *intuitive mental model*



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

### Dude, do you even know what TLDR  means?

Damn.
