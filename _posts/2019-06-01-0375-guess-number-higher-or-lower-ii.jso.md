---
layout: post
title: 375. Guess Number Higher or Lower II
---
### Question
We are playing the Guess Game. The game is as follows:

I pick a number from **1** to **n**. You have to guess which number I picked.

Every time you guess wrong, I'll tell you whether the number I picked is
higher or lower.

However, when you guess a particular number x, and you guess wrong, you pay
**$x**. You win the game when you guess the number I picked.

 **Example:**

    
    
    n = 10, I pick 8.
    
    First round:  You guess 5, I tell you that it's higher. You pay $5.
    Second round: You guess 7, I tell you that it's higher. You pay $7.
    Third round:  You guess 9, I tell you that it's lower. You pay $9.
    
    Game over. 8 is the number I picked.
    
    You end up paying $5 + $7 + $9 = $21.
    

Given a particular **n ≥ 1** , find out how much money you need to have to
guarantee a **win**.

### Solution 1
For each number x in range[i~j]  
we do: result_when_pick_x = x + **max** {DP([i~x-1]), DP([x+1, j])}  
\--> _// the max means whenever you choose a number, the feedback is always
bad and therefore leads you to a worse branch._  
then we get DP([i~j]) = **min** {xi, ... ,xj}  
\--> _// this min makes sure that you are minimizing your cost._

    
    
    public  class Solution {
        public int getMoneyAmount(int n) {
            int[][] table = new int[n+1][n+1];
            return DP(table, 1, n);
        }
        
        int DP(int[][] t, int s, int e){
            if(s >= e) return 0;
            if(t[s][e] != 0) return t[s][e];
            int res = Integer.MAX_VALUE;
            for(int x=s; x<=e; x++){
                int tmp = x + Math.max(DP(t, s, x-1), DP(t, x+1, e));
                res = Math.min(res, tmp);
            }
            t[s][e] = res;
            return res;
        }
    }
    

Here is a bottom up solution.

    
    
    public class Solution {
        public int getMoneyAmount(int n) {
            int[][] table = new int[n+1][n+1];
            for(int j=2; j<=n; j++){
                for(int i=j-1; i>0; i--){
                    int globalMin = Integer.MAX_VALUE;
                    for(int k=i+1; k<j; k++){
                        int localMax = k + Math.max(table[i][k-1], table[k+1][j]);
                        globalMin = Math.min(globalMin, localMax);
                    }
                    table[i][j] = i+1==j?i:globalMin;
                }
            }
            return table[1][n];
        }
    }
    


### Solution 2
Can you guys define the problem a little more and improve the test case.

The example provided for n=10 does not actually give the solution to the
problem (it shows 21 as the result of the provided guessing pattern); however,
if you run a custom testcase the leetcode server say the answer is 16. Can you
explain how we arrive at 16. If the number is 8 and we choose 5 7 9, we pay
$21. How does one do better than that?

Additionally, the question states: Given a particular n ≥ 1, find out how much
money you need to have to guarantee a win. Well, this wording is ambiguous, as
I should be able to always return n(n+1)/2 to give the sum of all numbers from
1 to n. That would certainly guarantee a win, right? I think you are looking
for the **minimum** amount of money you need to guarantee a win. If so, can
you specify that and improve the example to reflect it (and show how the
answer 16 is achieved).


### Solution 3
It is actually confusing that the example shown in the problem description is
not the best stragety to guess the final target number, and the problem itself
is asking for the lowest cost achieved by best guessing strategy.  
The example description should be updated.

`---POSSIBLY, it can also add some example about the BEST Strategy---`  
The example description should be:

first introducebest strategyto guess:

  1. `for one number`, like 1, best strategy is 0$
  2. `for two number`, like 3,4, best strategy is 3$, which can be understood in this way: you have two way to guess: a) start by guess 4 is the target, (the worst case is) if wrong, you get charged $4, then immediately you know 3 is the target number, get get charged $0 by guessing that, and finally you get charged $4. b) similarly, if you start by 3, (the worst case is) if wrong, you get charged $3, then you immediately know that 4 is the target number, and get charged $0 for guessing this, and finally you get charged $3. In summary:  
range ---------> best strategy cost  
3, 4 ---------> $3  
5, 6 ---------> $5  
...

  3. `for three number`, the best strategy is guess the middle number first, and (worst case is) if wrong, you get charged that middle number money, and then you immediately know what target number is by using "lower" or "higher" response, so in summary:  
range ---------> best strategy cost  
3, 4, 5 ---------> $4  
7, 8, 9 ---------> $8  
...

  4. `for more numbers`, it can simply be reduced them into smaller ranges, and here is why DP solution make more sense in solving this.  
suppose the range is [start, end]  
the strategy here is to iterate through all number possible and select it as
the starting point, say for any k between start and end, the worst cost for
this is: k+DP( start, k-1 ) + DP(k+1, end ), and the goal is minimize the
cost, so you need the minimum one among all those k between start and end



