---
layout: post
title: Leetcode - Stock Problems
subtitle: Best Time to Buy and Sell Stock 
date: 2016-12-22
categories: Leetcode
tags: [Array, DP]
catalog: true
---

###  LC121. Best Time to Buy and Sell Stock

One transaction. O(n). 

Find min buying day and max selling day.

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int max = 0;
        int buy = Integer.MAX_VALUE;
        for(int i = 0; i < prices.length; i++) {
            if(prices[i] < buy){
                buy = prices[i];
            }
            if(prices[i] - buy > max) {
                max = prices[i] - buy;
            }
        } // for i
        return max;
    }
}
```

### LC122. Best Time to Buy and Sell Stock II

As many transactoin as you like. O(n).

Each time making profit, making profit.

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int result = 0;
        int dif = 0;
        for(int i = 0; i < prices.length-1; i++) {
            dif = prices[i+1]-prices[i];
            result = dif>0?dif+result:result;
        }
        return result;
    }
}
```

### LC123. Best Time to Buy and Sell Stock III

At most ***two*** transactions. DP problems. O(n), O(1).

```java
public class Solution {
    public int maxProfit(int[] prices) {
        // dp - buy OR not
        // the largest revnue happens when we sold all the stock
        // so we have revenue1, revenue2, hold1, hold2
        int revenue1 = 0, revenue2 = 0;
        int hold1 = Integer.MIN_VALUE, hold2 = Integer.MIN_VALUE;
        for(int p: prices){
            revenue1 = Math.max(revenue1, hold1+p); // after we sold 1st stock
            hold1 = Math.max(-p, hold1); // after we bought 1st stock
            revenue2 = Math.max(revenue2, hold2+p); // after we sold 2nd stock
            hold2 = Math.max(revenue1-p, hold2); // after we bought 2nd stock
        } // for p
        return Math.max(revenue1, revenue2);
    }
}
```

### LC188. Best Time to Buy and Sell Stock IV

At most ***k*** transactions. DP problem. O(n), O(k).

> When k > n/2, is LC122. [Discuss](https://discuss.leetcode.com/topic/8984/a-concise-dp-solution-in-java)

```java
public class Solution {
    public int maxProfit(int k, int[] prices) {
        if(k == 0 || prices == null || prices.length == 0)  return 0;
        // we have k hold and k revenue
        int len = prices.length;
        if(k >= len/2)  return quickProfit(prices);
        // hold[i] is the money after we bought ith stock
        int[] hold = new int[k];
        Arrays.fill(hold, Integer.MIN_VALUE);
        // revenue[i] is the money after we sold the ith stock
        int[] revenue = new int[k];
        for(int p: prices){
            revenue[0] = Math.max(revenue[0], hold[0]+p);
            hold[0] = Math.max(-p, hold[0]);
            for(int i = 1; i < k; i++){
                revenue[i] = Math.max(revenue[i], hold[i]+p);
                hold[i] = Math.max(revenue[i-1]-p, hold[i]);
            }
        }
        return revenue[k-1];
    }
    
    // we can make profit when there is change of price: brillient!
    public int quickProfit(int[] prices){
        int profit = 0;
        for (int i = 1; i < prices.length; i++)
            if (prices[i] > prices[i - 1]) profit += prices[i] - prices[i - 1];
        return profit;
    }
}
```

### LC309. Best Time to Buy and Sell Stock with Cooldown

As many transcations as you like. After selling, have one cooldown day cannot buy.

DP problem: O(n), O(1).

```java
// situation buy, sell, rest
// buy[i] = max(rest[i-1]-price, buy[i-1]);
// rest[i] = max(sell[i-1], buy[i-1], rest[i-1]);
// sell[i] = max(buy[i-1]+price, sell[i-1]);
// rest[i] = sell[i-1];

public class Solution {
    public int maxProfit(int[] prices) {
        int prev_sell = 0, prev_buy = 0, sell = 0, buy = Integer.MIN_VALUE;
        for(int price: prices) {
            prev_buy = buy;
            buy = Math.max(prev_sell-price, prev_buy);
            prev_sell = sell;
            sell = Math.max(prev_buy+price, prev_sell);
        } // for
        return sell;
    }
}
```

Done~👻