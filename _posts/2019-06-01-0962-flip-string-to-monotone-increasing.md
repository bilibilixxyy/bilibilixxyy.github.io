---
layout: post
title: 962. Flip String to Monotone Increasing
---
### Question
A string of `'0'`s and `'1'`s is _monotone increasing_ if it consists of some
number of `'0'`s (possibly 0), followed by some number of `'1'`s (also
possibly 0.)

We are given a string `S` of `'0'`s and `'1'`s, and we may flip any `'0'` to a
`'1'` or a `'1'` to a `'0'`.

Return the minimum number of flips to make `S` monotone increasing.



 **Example 1:**

    
    
     **Input:** "00110"
    **Output:** 1
    **Explanation:** We flip the last digit to get 00111.
    

**Example 2:**

    
    
    **Input:** "010110"
    **Output:** 2
    **Explanation:** We flip to get 011111, or alternatively 000111.
    

**Example 3:**

    
    
    **Input:** "00011000"
    **Output:** 2
    **Explanation:** We flip to get 00000000.
    



 **Note:**

  1. `1 <= S.length <= 20000`
  2. `S` only consists of `'0'` and `'1'` characters.

### Solution 1
Algorithm:

  1. Skip 0's until we encounter the first 1.
  2. Keep track of number of 1's in onesCount (Prefix).
  3. Any 0 that comes after we encounter 1 can be a potential candidate for flip. Keep track of it in flipCount.
  4. If flipCount exceeds oneCount - (Prefix 1's flipped to 0's)  
a. Then we are trying to flip more 0's (suffix) than number of 1's (prefix) we
have.  
b. Its better to flip the 1's instead.

    
    
    public int minFlipsMonoIncr(String S) {
    	if(S == null || S.length() <= 0 )
    		return 0;
    
    	char[] sChars = S.toCharArray();
    	int flipCount = 0;
    	int onesCount = 0;
    
    	for(int i=0; i<sChars.length; i++){
    		if(sChars[i] == '0'){
    			if(onesCount == 0) continue;
    			else flipCount++;
    		}else{
    			onesCount++;
    		}
    		if(flipCount > onesCount){
    			flipCount = onesCount;
    		}
    	}
    	return flipCount;
    }
    


### Solution 2
We need to split the array into left '0' and right '1' sub-arrays, so that sum
of '1' -> '0' flips (left) and '0' -> '1' flips (right) is minimal.

  * Count of '0' -> '1' flips going left to right, and store it in `f0`.
  * Count of '1' -> '0' flips going right to left, and store it in `f1`.
  * Find a the smallest `f0[i] + f1[i]`.

    
    
    int minFlipsMonoIncr(string S, int res = INT_MAX) {
        vector<int> f0(S.size() + 1), f1(S.size() + 1);
        for (int i = 1, j = S.size() - 1; j >= 0; ++i, --j) {
            f0[i] += f0[i - 1] + (S[i - 1] == '0' ? 0 : 1);
            f1[j] += f1[j + 1] + (S[j] == '1' ? 0 : 1);
        }
        for (int i = 0; i <= S.size(); ++i) res = min(res, f0[i] + f1[i]);
        return res;
    }
    

![image](https://assets.leetcode.com/users/votrubac/image_1545866108.png)  
This reminds me of [122\. Best Time to Buy and Sell Stock
III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-
iii/description/). That problem has a DP solution with O(1) memory complexity,
so we can try to apply it here. We can go left to right, and virtually 'move'
the split point every time we see that we need less '0' -> '1' than '1' -> '0'
flips (`f1 = min(f0, f1 + 1 - (c - '0'))`).

    
    
    int minFlipsMonoIncr(string S, int f0 = 0, int f1 = 0) {
        for (auto c : S) {
            f0 += c - '0';
            f1 = min(f0, f1 + 1 - (c - '0'));
        }
        return f1;
    }
    

Here is Java version. Note that I am using `charAt(i)` instead of
`toCharArray()` to keep the memory complexity a constant.

    
    
    public int minFlipsMonoIncr(String S) {
      int f0 = 0, f1 = 0;
      for (int i = 0; i < S.length(); ++i) {
        f0 += S.charAt(i) - '0';
        f1 = Math.min(f0, f1 + 1 - (S.charAt(i) - '0'));
      }
      return f1;
    }
    


### Solution 3
    
    
    /* tranverse the string
     * use two variables to record the minimim number of flip need to make the substring from (0 - i) to be MonoIncr with end with 1 or 0;
     * (1) for position i + 1, if preceding string is end with 1, the current string can only end with one, 
     *   so cntEndWithOne can only be used to update the next cntEndWithOne
     * (2) if the preceding string is end with zero, current string can either end with zero or one
     *   so cntEndWithZero can be used to update for both next cntEndWithOne and cntEndWithZero;
     */
    class Solution {    
        public int minFlipsMonoIncr(String S) {
            if (S == null || S.length() <= 1) return 0;
            int n = S.length();
            int cntEndWithOne = 0, cntEndWithZero = 0;
            
            for (int i = 0; i < n; i++) {
                char c = S.charAt(i);
                cntEndWithOne = Math.min(cntEndWithZero, cntEndWithOne) + (c == '1' ? 0 : 1);
                cntEndWithZero += (c == '0' ? 0 : 1);
            }
            return Math.min(cntEndWithOne, cntEndWithZero);
        }
    }



