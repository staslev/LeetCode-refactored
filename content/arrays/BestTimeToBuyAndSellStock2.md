## Best Time to Buy and Sell Stock II
>Say you have an array for which the *i*th element is the price of a given stock on day *i*.
>
>Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).
>
>**Note:** You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).
>
>**Example 1:**
>
>```
>Input: [7,1,5,3,6,4]
>Output: 7
>Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
>```
>
>**Example 2:**
>
>```
>Input: [1,2,3,4,5]
>Output: 4
>Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
>Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are engaging multiple transactions at the same time. You must sell before buying again.
>```
>
>**Example 3:**
>
>```
>Input: [7,6,4,3,1]
>Output: 0
>Explanation: In this case, no transaction is done, i.e. max profit = 0.
>```
>
>Solution signature: `int maxProfit(int[] prices)`
>
>[Try it on LeetCode.](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)



### Strategy

Since we can perform as many transactions as we wish, and we know the price list for the entire period, this is basically every trader's dream scenario. The guiding principle is that whenever the price goes up, we buy the stock the previous day, only to sell it the next day and make a profit equals to the price diff.



### Code

This can be expressed in code like so, copied from LeetCode's discussion section:

```java
// soloution version 1 (seen online)
public int maxProfit(int[] p) {
  int res = 0;
  for(int i = 1; i < p.length; i++) {
    if(p[i] > p[i-1]) {
      res += p[i]-p[i-1];
    }
  }
  return res;
}
```


### Refactored

The solution on LeetCode is quite concise but I believe naming could be better. In addition, personally I get the shivers whenever I see if-clauses inside loops so I tend to look for ways to get rid of those.

```java
// soloution version 1, restructured.
public int maxProfit(int[] prices) {
  int totalProfit = 0;  
  for (int day = 1; day < prices.length; day++) {  
    int dailyGain = Math.max(prices[day] - prices[day - 1], 0);
    totalProfit += dailyGain;
  }
  return totalProfit;  
}
```

This code reflects our trading strategy in a straight forward manner. 
We scan the price list starting from the second day (`day = 1`), and calculate the potential daily gain, which equals to the difference between today's price and yesterday's price. If the gain is positive, we make the trade and increase the total profit by the diff we've gained. If the gain is negative, we do not make the transaction, which is equivalent to gaining zero.

By having `totalProfit += Math.max(0, dailyGain)` we eliminate the need for a conditional expression inside the loop. It's a way of saying that there is always *some*  `dailyGain`, it's just a question of whether it's positive or negative. When it's negative, we don't make a transaction and gain `0` (instead of making a transaction that will lose money). 
Also worth noting is that the `dailyGain` variable can be inlined, but I believe its presence makes for a clearer code.

The solution can also be expressed using Java streams. I wouldn't say it's better than the previous solution but just for kicks:

```java
// soloution version 1, restructured + streams.
public int maxProfit(int[] prices) {  
  return IntStream.range(1, prices.length)  
   .map(day -> Math.max(prices[day] - prices[day - 1], 0))  
   .sum();  
}
```
