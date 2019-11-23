---
layout: post
title: 121. Best Time to Buy and Sell Stock
---
### Question
Say you have an array for which the _i_ th element is the price of a given
stock on day _i_.

If you were only permitted to complete at most one transaction (i.e., buy one
and sell one share of the stock), design an algorithm to find the maximum
profit.

Note that you cannot sell a stock before you buy one.

 **Example 1:**

    
    
     **Input:** [7,1,5,3,6,4]
    **Output:** 5
    **Explanation:** Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
                  Not 7-1 = 6, as selling price needs to be larger than buying price.
    

**Example 2:**

    
    
    **Input:** [7,6,4,3,1]
    **Output:** 0
    **Explanation:** In this case, no transaction is done, i.e. max profit = 0.
    

### Solution 1
The logic to solve this problem is same as "max subarray problem" using
`Kadane's Algorithm`. Since no body has mentioned this so far, I thought it's
a good thing for everybody to know.

All the straight forward solution should work, but if the interviewer twists
the question slightly by giving the _**difference array of prices**_ , Ex: for
`{1, 7, 4, 11}`, if he gives `{0, 6, -3, 7}`, you might end up being confused.

Here, the logic is to calculate the difference (`maxCur += prices[i] -
prices[i-1]`) of the original array, and find a contiguous subarray giving
maximum profit. If the difference falls below 0, reset it to zero.

    
    
        public int maxProfit(int[] prices) {
            int maxCur = 0, maxSoFar = 0;
            for(int i = 1; i < prices.length; i++) {
                maxCur = Math.max(0, maxCur += prices[i] - prices[i-1]);
                maxSoFar = Math.max(maxCur, maxSoFar);
            }
            return maxSoFar;
        }
    

*`maxCur = current maximum value`

*`maxSoFar = maximum value found so far`


### Solution 2
    
    
    int maxProfit(vector<int> &prices) {
        int maxPro = 0;
        int minPrice = INT_MAX;
        for(int i = 0; i < prices.size(); i++){
            minPrice = min(minPrice, prices[i]);
            maxPro = max(maxPro, prices[i] - minPrice);
        }
        return maxPro;
    }
    

minPrice is the minimum price from day 0 to day i. And maxPro is the maximum
profit we can get from day 0 to day i.

How to get maxPro? Just get the larger one between current maxPro and
prices[i] - minPrice.


### Solution 3
"If you were only permitted to complete at most one transaction (ie, buy one
and sell one share of the stock), design an algorithm to find the maximum
profit."

I misunderstood the above sentence as at most either buy or sell one share of
stock on a single day. In that case, I buy at a local minimum price and sell
it at a local maximum to reach a maximum total profit.

Please consider changing it to something like the following:

`If you were only permitted to complete at most one buy and at most one sell
during the whole period of days, design an algorithm such that your profit,
i.e., sell price minus buy price, is maximized. Note that you can only not
sell a stock before you buy one.`

`For example:`

`[1,2,3,4] ==> returns 3 (buy at 1 and sell at 4)`

`[4,3,2,1] ==> returns 0 (don't buy)`

`[4,10,25,2,10] ==> returns 21 (buy at 4 and sell at 25)`



