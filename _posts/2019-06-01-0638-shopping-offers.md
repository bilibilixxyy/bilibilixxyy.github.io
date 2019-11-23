---
layout: post
title: 638. Shopping Offers
---
### Question
In LeetCode Store, there are some kinds of items to sell. Each item has a
price.

However, there are some special offers, and a special offer consists of one or
more different kinds of items with a sale price.

You are given the each item's price, a set of special offers, and the number
we need to buy for each item.The job is to output the lowest price you have to
pay for **exactly** certain items as given, where you could make optimal use
of the special offers.

Each special offer is represented in the form of an array, the last number
represents the price you need to pay for this special offer, other numbers
represents how many specific items you could get if you buy this offer.

You could use any of special offers as many times as you want.

 **Example 1:**  

    
    
     **Input:** [2,5], [[3,0,5],[1,2,10]], [3,2]
    **Output:** 14
    **Explanation:** 
    There are two kinds of items, A and B. Their prices are $2 and $5 respectively. 
    In special offer 1, you can pay $5 for 3A and 0B
    In special offer 2, you can pay $10 for 1A and 2B. 
    You need to buy 3A and 2B, so you may pay $10 for 1A and 2B (special offer #2), and $4 for 2A.
    

**Example 2:**  

    
    
    **Input:** [2,3,4], [[1,1,0,4],[2,2,1,9]], [1,2,1]
    **Output:** 11
    **Explanation:** 
    The price of A is $2, and $3 for B, $4 for C. 
    You may pay $4 for 1A and 1B, and $9 for 2A ,2B and 1C. 
    You need to buy 1A ,2B and 1C, so you may pay $4 for 1A and 1B (special offer #1), and $3 for 1B, $4 for 1C. 
    You cannot add more items, though only $9 for 2A ,2B and 1C.
    

**Note:**  

  1. There are at most 6 kinds of items, 100 special offers.
  2. For each item, you need to buy at most 6 of them.
  3. You are **not** allowed to buy more items than you want, even if that would lower the overall price.

### Solution 1
The idea is very similar to combination sum. In combination sum where each
element can be repeated, check each element to see if it can be used (in this
case, if the sum doesn't exceed the target). If so, use it. Repeat this until
we get the result.

For this question, we check each promotion offers, if the offer can be used,
use it. Repeat the process and find the minimum result. In this question, the
condition whether one offer can be used is the number of items in the offer
doesn't exceed the needed number. Find the minimum among all combinations.

The thing to remember, which also happens in real life is that some special
offers are actually more expensive than buying each individual item in the
offers!!! Thus be smart and compare if buying directly is cheaper.

here is my code:

    
    
    public class Solution {
        public int shoppingOffers(List<Integer> price, List<List<Integer>> special, List<Integer> needs) {
        	return helper(price, special, needs, 0);
        }
        
        private int helper(List<Integer> price, List<List<Integer>> special, List<Integer> needs, int pos) {
        	int local_min = directPurchase(price, needs);
        	for (int i = pos; i < special.size(); i++) {
        		List<Integer> offer = special.get(i);
        		List<Integer> temp = new ArrayList<Integer>();
            	for (int j= 0; j < needs.size(); j++) {
            		if (needs.get(j) < offer.get(j)) { // check if the current offer is valid
            			temp =  null;
            			break;
            		}
            		temp.add(needs.get(j) - offer.get(j));
            	}
            	
        		if (temp != null) { // use the current offer and try next
        			local_min = Math.min(local_min, offer.get(offer.size() - 1) + helper(price, special, temp, i)); 
        		}
        	}
    
        	return  local_min;
        }
        
        private int directPurchase(List<Integer> price, List<Integer> needs) {
        	int total = 0;
        	for (int i = 0; i < needs.size(); i++) {
        		total += price.get(i) * needs.get(i);
        	}
        	
        	return total;
        }
    }
    
    


### Solution 2
    
    
    int shoppingOffers(vector<int>& price, vector<vector<int>>& special, vector<int>& needs, int cost = 0) {
        if (needs < 0)
            return INT_MAX;
    
        int m = inner_product(needs.begin(), needs.end(), price.begin(), cost);
    
        for (auto &offer : special) {
            if (cost + offer.back() >= m) // pruning
                continue;
            needs -= offer;
            m = min(m, shoppingOffers(price, special, needs, cost + offer.back()));
            needs += offer;
        }
    
        return m;
    }
    

with the following operators defined above the Solution class:

    
    
    void operator+=(vector<int> &a, const vector<int> &b) {
        for (int i = 0; i < a.size(); i++)
            a[i] += b[i];
    }
    
    void operator-=(vector<int> &a, const vector<int> &b) {
        for (int i = 0; i < a.size(); i++)
            a[i] -= b[i];
    }
    
    bool operator<(const vector<int> &a, const int &n) {
        for (int i : a)
            if (i < n)
                return true;
        return false;
    }
    


### Solution 3
    
    
    def shoppingOffers(self, price, special, needs):
            d = {}
            def dfs(cur):
                val = sum(cur[i]*price[i] for i in range(len(needs))) #cost without special
                for spec in special:
                    tmp = [cur[j] - spec[j] for j in range(len(needs))]
                    if min(tmp) >= 0: # skip deals that exceed needs
                        val = min(val, d.get(tuple(tmp), dfs(tmp)) + spec[-1]) # .get check the dictionary first for result, otherwise perform dfs.
                d[tuple(cur)] = val
                return val
            return dfs(needs)
    



