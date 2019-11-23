---
layout: post
title: 1055. Pairs of Songs With Total Durations Divisible by 60
---
### Question
In a list of songs, the `i`-th song has a duration of `time[i]` seconds.

Return the number of pairs of songs for which their total duration in seconds
is divisible by `60`.  Formally, we want the number of indices `i < j` with
`(time[i] + time[j]) % 60 == 0`.



 **Example 1:**

    
    
     **Input:** [30,20,150,100,40]
    **Output:** 3
    **Explanation:** Three pairs have a total duration divisible by 60:
    (time[0] = 30, time[2] = 150): total duration 180
    (time[1] = 20, time[3] = 100): total duration 120
    (time[1] = 20, time[4] = 40): total duration 60
    

**Example 2:**

    
    
    **Input:** [60,60,60]
    **Output:** 3
    **Explanation:** All three pairs have a total duration of 120, which is divisible by 60.
    



 **Note:**

  1. `1 <= time.length <= 60000`
  2. `1 <= time[i] <= 500`

### Solution 1
## **Intuition**

Calculate the `time % 60` then it will be exactly same as two sum problem.  
  

##  **Explanation**

`t % 60` gets the remainder **0 ~ 59**.  
We count the occurrence of each remainders in a array/hashmap `c`.  
we want to know that, for each `t`, how many `x` satisfy `(t + x) % 60 = 0`.

One idea is take `x % 60 = 60 - t % 60`, which is valid for the most cases.  
But if `t % 60 = 0`, `x % 60 = 0` instead of 60.  
`60 - t % 60` will get a number in range **1 ~ 60**.  
`(60 - t % 60) % 60` can get number in range **0 ~ 59** , which is what we
want.

Another idea is that `x % 60 = (600 - t) % 60`.  
Not sure which one is more straight forward.

  

 **Java:**

    
    
         public int numPairsDivisibleBy60(int[] time) {
            int c[]  = new int[60], res = 0;
            for (int t : time) {
                res += c[(600 - t) % 60];
                c[t % 60] += 1;
            }
            return res;
        }
    

**C++:**

    
    
         int numPairsDivisibleBy60(vector<int>& time) {
            vector<int> c(60);
            int res = 0;
            for (int t : time) {
                res += c[(600 - t) % 60];
                c[t % 60] += 1;
            }
            return res;
        }
    

**Python:**

    
    
        def numPairsDivisibleBy60(self, time):
            c = collections.Counter()
            res =  0
            for t in time:
                res += c[-t % 60]
                c[t % 60] += 1
            return res
    


### Solution 2
**Intuition**

  * The problem can be reduced to modular arithmetic by doing every operation `mod 60`. Since every number divisible by 60 becomes `0` in the modular arithemtic of 60, the equivalent characterization of the problem is, **How many pairs sum upto 0 (in modular arithmetic of 60)**.
  * Notice that there are only 60 different numbers in the modular arithmetic. i.e `[0,1,2,...59]`.
  * We traverse the array and update the count of each of the 60 numbers. (How?). Any number `num` contributes to `num%60` in the modular version. So we increase the count of `num%60` each time we encounter an element.
  * Now that we only have numbers from `0...59`, let us see which numbers can sum up to zero.
  * Clearly each `0` can be paired with another zero to sum up to zero. How many such pairs are there? Suppose the count of zeros is `n`. Then selecting any 2 elements out of this would result in an answer. How many ways are there to select? Clearly, `nC2` = `n*(n-1)/2`.
  * Similarly, `30` can be paired with `30` to give `0` (since `30+30` equals to `0` mod `60`). How many pairs can we form from 30? As argued above, `nC2` = `n*(n-1)/2`, where `n` is the frequency of `30` in the map.
  * Now, for the remaining elements, how many ways are there to sum upto `0`? Let's pick a number `a`. The complement of this number is `b` = `60-a`. Suppose the frequency of `a` is `m` and the frequency of `b` is `n`. Now, picking any one `a` and any one `b` would sum upto 0. How many ways are possible to pick one element from each set ? Clearly `m*n`.
  * Care has to be taken to avoid recounting the pairs `(a,b)` and `(b,a)`

 **Algorithm**

  * After updating the count of each of the 60 numbers in the map, we traverse the numbers `[1,2,3...29]`. For each element `a` we find the frequency of `a` and the frequency of its complement and increment the answer by their product.
  * As discussed above, we deal with `0` and `30` separately as they are their own complements.

 **Miscellaneous**

  * We only loop upto 29 so as to avoid recounting. (Beacause every number greater than 30 has a complement less than 30, which has already been counted in the `for` loop).

    
    
    class Solution
    {
    public:
        int numPairsDivisibleBy60(vector<int>& time);
    };
    
    /* Returns the number of pairs whose sum is divisible by 60 */
    int Solution :: numPairsDivisibleBy60(vector<int>& a)
    {
    	// The reference for modular arithmetic
        int ref=60;
    	
    	// Populate the map in modular arithemtic
        unordered_map<int,int> count;
        for(auto ele : a)
            count[ele%ref]++;
    		
        // Traverse from 1 to 29 and update answer
        int answer=0;
        for(int i=1; i<ref/2; i++)
            answer += count[i]*count[ref-i];
    		
        // Deal with numbers with identical complement
        answer += count[0]*(count[0]-1)/2;
        answer += count[ref/2]*(count[ref/2]-1)/2;
        
        return answer;
    }
    

[JavaScript Implementation](https://github.com/Just-A-
Visitor/Coding/tree/master/LeetCode%20Contests/LeetCode%20Contest%20%23128/%231013%20Pairs%20with%20Sum%20divisible%20by%2060)


### Solution 3
Let `target` in Two Sum be 60 and each item in `time` % 60, the two problems
are very similar to each other.

    
    
        public int numPairsDivisibleBy60(int[] time) {
            Map<Integer, Integer> count = new HashMap<>();
            int ans = 0;
            for (int t : time) {
                int d = (60 - t % 60) % 60;
                ans += count.getOrDefault(d, 0); // in current HashMap, get the number of songs that if adding t equals to a multiple of 60.
                count.put(t % 60, 1 + count.getOrDefault(t % 60, 0)); // update the number of t % 60.
            }
            return ans;
        }
    



