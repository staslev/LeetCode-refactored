# Assorted LeetCode solutions



## Does the world really need another LeetCode solution set?

Probably not. Who knows what the world really needs these days.

What I have noticed though, is that existing solution sets, and there's plenty of them on GitHub, tend to focus purely on the algorithmic nature of the problem. Consequently, I believe many of these solutions leave much to be desired as far as *software engineering* is concerned.

It could actually be funny, if it wasn't  so tragic, because I presume most of the people who wind up on LeetCode probably do so in order to prepare for an interview for a software engineering position and eventually professionally practise software engineering. 
What brings things back to the funny zone is that many corporate interviewers put little emphasis on actually checking for software engineering qualities in favour of whiteboard skills, resulting in a process optimised for the survival of whiteboard engineers.

It never ceases to amaze me, how often, and to what extent, common solutions are under software-engineered. Now, I'm by no means saying that these solutions aren't clever, or even occasionally genius, what I'm saying is that things like code structure & readability seem to be the first causalities in this corporate whiteboard interview tragedy.

Personally I happen to believe in software engineering practices, and wish more of them could be taken into account when educating, and interviewing, software engineers.  To that end, my goal with this project it to try to present a different angle on LeetCode solutions which incorporates some of my beliefs and perspectives, gathered over the past decade and a half of professionally practicing and researching (as in a P.hD.) software engineering. 

In particular I strongly believe in:
 - A very low tolerance for unreadable code
 - Code that reflects an *intuitive* mental model of the solution



## That's a lot of words. TLDR please?

> **Move Zeros**
> Given an array `nums`, write a function to move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.
>
> Solution signature is: `void moveZeroes(int[] nums)`



A solution can be easily picked up from LeetCode's discussion section:
```java
class Solution {
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
}
```

It's clean indeed, but could use a little structure.  In fact, if we slightly refactor it, this solution will be able to tell us what actually needs to be done as part of the solution:

```java
class Solution {  
	public void moveZeroes(int[] nums) {  
		int firstNonZeroIndex = moveNonZerosToStart(nums); // check
		fillZeros(nums, firstNonZeroIndex); // mate
	}
}
```

 `moveNonZerosToStart` takes care of moving all non zero integers to the start of the array, and returning the first index after the last non zero integer:
```java
private int moveNonZerosToStart(int[] nums) {  
	int firstNonZeroIndex = 0;  
	for (int num : nums) {  
		if (num != 0) {  
			nums[firstNonZeroIndex++] = num;  	  
		}
	}  
	return firstNonZeroIndex;  
} 
```
`fillZeros` makes sure to fill the remainder of the array with zeros, like so:
```java
private void fillZeros(int[] nums, int start) {  
	for (int i = start; i < nums.length; i++) {  
		nums[i] = 0;  
	}
}  
```



#### So why is the latter better than the former?

Mostly because when one reads the revised version of the `moveZeroes` method, the steps towards the solution become blatantly clear. Each step's logic can then be isolated and implemented independently. 

Looking at the original solution, this is hardly the case. Even though it's clean, the cognitive load is far from trivial given the loops and the conditional expression in one of them.

Moreover, in some cases the refactored solutions may be somewhat longer than the original ones I copy paste from LeetCode. However, I believe that the refactored versions are at least as clearer as they are longer. Probably even more so.

It's also worth noting that a solution being shorter, does not necessarily mean it is easier to produce during an interview. Perhaps somewhat surprisingly, I'd argue it is often the other way around. Longer, but significantly clearer solution could be easier to reproduce since they reflect a clear mental model of the solution. 

Shorter solutions may be easier to memorize, but that may not be the best approach towards technical interviews.



## Dude, do you even know what TLDR  means?
Damn.



## Ok, Ok. Show me more.
Good thing we have a [table of contents](toc.md).
