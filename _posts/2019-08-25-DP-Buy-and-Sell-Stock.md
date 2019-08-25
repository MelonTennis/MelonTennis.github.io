---
layout: post
title: Leetcode - Buy and Sell Stock
subtitle: LC121, LC122, LC123, LC188, LC309, LC714
date: 2019-08-25
categories: Leetcode
tags: [DP]
description: 
catalog: true
---

## Best Time to Buy and Sell Stock

Leetcode DP tag下的一个买股票系列问题。解决方法是1d动态规划，只要能够想清楚状态转换还是很好解决的。

### Best Time to Buy and Sell Stock I - Easy

限定一次交易，求所得最大。O(n), O(1)，跑一遍即可。

```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices == null || prices.length == 0)    return 0;
        int n = prices.length, buy = prices[0], max = 0;
        for(int i = 0; i < n; i++) {
            if(prices[i] < buy) {
                buy = prices[i];
            }
            max = Math.max(max, prices[i] - buy);
        }
        return max;
    }
}
```



### Best Time to Buy and Sell Stock II - Easy

无数次交易，求利润最大。扫一遍，当买入值可以更低就更新买入值，否则就卖出。O(n), O(1).

```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices == null || prices.length == 0)    return 0;
        int buy = prices[0], sum = 0;
        for(int i = 1; i < prices.length; i++) {
            if(prices[i] > buy) {
                sum += prices[i] - buy;
                buy = prices[i];
            } else {
                buy = prices[i];
            }
        }
        return sum;
    }
}
```



### Best Time to Buy and Sell Stock III - Hard

最多完成两次交易。这里拥有4种状态，买1后，卖1后，买2后，卖2后。对于每个price，都有可能存在这四个状态，因此都更新到最大值，最后max(卖1, 卖2)就是答案。买1和买2的初始化是Integer.MIN_VALUE,因为买1要和-price进行比较。这里因为只用了4个变量所以要先更新卖1和卖2，因为卖1更新来自于上一个买1，同理卖2的更新在买1之后，否则就需要8个变量。O(1), (1).

Array for  *i*th price for day i. Find the maximum profit within two transactions. Stock must be sold before buying again.

**Analysis**

Typical DP question. Using 4 vaiables to represent the revenue after selling 1st and 2nd stock and the hold on value after buying 1st and 2nd stock. 

Revenue1 = max(revenue1, hold1 + boughtPrice)

Hold1 = max(hold1, -boughtPrice) 

Revenue2 = max(revenue2, hold2 + boughtPrice)

Hold2 = max(revenue1 - boughtPrice, hold2)

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



### Best Time to Buy and Sell Stock IV - Hard

把III的最多两次交易变成最多K次交易，因此状态需要K*2的数组来存。表示买的状态初始化为Integer.MIN_VALUE.同理对于每一个price，按次序更新卖i，买i，直到卖k，买k。O(n), O(K). 这里有一点是如果可以完成n／2次交易那么表示每一个可能存在的利润都可以得到，变成II。

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        if(prices == null || prices.length <= 1 || k == 0){
            return 0;
        }
        // if k > n/2, can get all profits
        if(k > prices.length/2){
            return quickSolve(prices);
        }
        int[] hold = new int[k];
        int[] revenue = new int[k];
        Arrays.fill(hold, Integer.MIN_VALUE);
        for(int p: prices){
            revenue[0] = Math.max(hold[0] + p, revenue[0]);
            hold[0] = Math.max(hold[0], -p);
            for(int i = 1; i < k; i++){
                revenue[i] = Math.max(hold[i] + p, revenue[i]);
                hold[i] = Math.max(revenue[i-1] - p, hold[i]);
            }
        }
        int max = 0;
        for(int r: revenue){
            if(max < r) max = r;
        }
        return max;
    }
    
    public int quickSolve(int[] prices){
        int res = 0;
        for(int i = 1; i < prices.length; i++){
            if(prices[i] > prices[i-1]){
                res += prices[i] - prices[i-1];
            }
        }
        return res;
    }
}
```



### Best Time to Buy and Sell Stock with Cooldown - Medium

II的问题但是刚卖过的第二天不可以买。对于每一个price，可能拥有3个状态，买，卖，cooldown。所求结果为最后一个price得到的卖的答案。可以知道买可由cooldown变成，卖可以由买变成，cooldown可以任意状态变成。因此：

buy = Math.max(pre_buy, pre_cooldown - price)

sell = Math.max(pre_buy + price, pre_sell)

cooldown = Math.max(pre_sell, pre_buy, pre_cooldown)

复杂度O(n), O(1)

```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices == null || prices.length == 0)    return 0;
        int sell = 0, buy = Integer.MIN_VALUE, rest = 0;
        int pre_sell = 0, pre_buy = Integer.MIN_VALUE, pre_rest = 0;
        for(int price: prices) {
            buy = Math.max(pre_rest - price, pre_buy);
            rest = Math.max(Math.max(pre_sell, pre_rest), pre_buy);
            sell = Math.max(pre_buy + price, pre_sell);
            pre_buy = buy;
            pre_rest = rest;
            pre_sell = sell;
        }
        return pre_sell;
    }
}
```



### Best Time to Buy and Sell Stock with Transaction Fee - Medium

II的问题加上一个transaction fee。那就在II的基础上每次买减去一个fee就好，利用动态规划很容易能够得到结果。O(n), O(1).

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        if(prices == null || prices.length == 0)    return 0;
        int buy = -prices[0], sell = 0;
        for(int i = 1; i < prices.length; i++) {
            int next_buy = Math.max(buy, sell - prices[i]);
            int next_sell = Math.max(sell, buy + prices[i] - fee);
            buy = next_buy;
            sell = next_sell;
        }
        return sell;
    }
}
```