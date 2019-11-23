---
layout: post
title: 1138. Grumpy Bookstore Owner
---
### Question
Today, the bookstore owner has a store open for `customers.length` minutes.
Every minute, some number of customers (`customers[i]`) enter the store, and
all those customers leave after the end of that minute.

On some minutes, the bookstore owner is grumpy.  If the bookstore owner is
grumpy on the i-th minute, `grumpy[i] = 1`, otherwise `grumpy[i] = 0`.  When
the bookstore owner is grumpy, the customers of that minute are not satisfied,
otherwise they are satisfied.

The bookstore owner knows a secret technique to keep themselves not grumpy for
`X` minutes straight, but can only use it once.

Return the maximum number of customers that can be satisfied throughout the
day.



 **Example 1:**

    
    
     **Input:** customers = [1,0,1,2,1,1,7,5], grumpy = [0,1,0,1,0,1,0,1], X = 3
    **Output:** 16
    **Explanation:**  The bookstore owner keeps themselves not grumpy for the last 3 minutes. 
    The maximum number of customers that can be satisfied = 1 + 1 + 1 + 1 + 7 + 5 = 16.
    



 **Note:**

  * `1 <= X <= customers.length == grumpy.length <= 20000`
  * `0 <= customers[i] <= 1000`
  * `0 <= grumpy[i] <= 1`

### Solution 1
  1. Use a sliding window `win` to record the number of satisfied customers if grumpy technique used for `X` minutes. Update the value of `win` when the window is wider than `X`.
  2. Use `satisfy` to record the number of satistified customers without grumpy technique.
  3. by the end of iterations, `satisfy` \+ `max(win)` is the answer.

    
    
        public int maxSatisfied(int[] customers, int[] grumpy, int X) {
            int satisfy = 0, max = 0;
            for (int i = 0, win = 0; i < grumpy.length; ++i) {
                if (grumpy[i] == 0) { satisfy += customers[i]; }
                else { win += customers[i]; }
                if (i >= X) {
                    win -= grumpy[i - X] * customers[i - X];
                }
                max = Math.max(win, max);
            }
            return satisfy + max;        
        }
    

**Analysis:**

Time: O(n), space: O(1), where n = grumpy.length;


### Solution 2
When the owner is not grumpy, we count all customers as `satisfied`.

We then use the sliding window to count additionally satisfied customers
(`add_satisfied`) if the owner start 'behaving' at minute `i`. We track the
maximum additional satisfied customers in `m_add_satisfied`.

Finally, return `satisfied + m_add_satisfied` as the result.

    
    
    int maxSatisfied(vector<int>& cs, vector<int>& grumpy, int X) {
        auto satisfied = 0, m_add_satisfied = 0, add_satisfied = 0;
        for (auto i = 0; i < cs.size(); ++i) {
            satisfied += grumpy[i] ? 0 : cs[i];
            add_satisfied += grumpy[i] ? cs[i] : 0;
            if (i >= X) add_satisfied -= grumpy[i - X] ? cs[i - X] : 0;
            m_add_satisfied = max(m_add_satisfied, add_satisfied);
        }
        return satisfied + m_add_satisfied;
    }
    

# Complexity Analysis

Runtime: O(n)  
Memory: O(1)


### Solution 3
The way I approached this problem was to split it into 2 smaller problems.

The first involves counting how many customers are already satisfied, i.e.
those where the shopkeeper is not grumpy. I also set any slots with already
satisfied customers to 0, so that we will be left with only the currently
unsatisfied customers in the list.

For the second part, the array now only contains customers who will not be
satisfied. We are able to make X adjacent times “happy”, so we want to find
the subarray of length X that has the most customers. We can just keep a
rolling sum of the last X customers in the array, and then the best solution
is the max the rolling sum ever was.

Finally we return the sum of the 2 parts: the customers who were already
satisfied, and the maximum number who can be made satisfied by stopping the
shop keeper from being grumpy for X time.

Note that both parts can be combined into a single loop, but I felt this way
was probably easier for understanding, and both are still `O(n)` time. I'll
shortly include code for how to merge it all together.

    
    
    class Solution:
        def maxSatisfied(self, customers: List[int], grumpy: List[int], X: int) -> int:
            
            # Part 1 requires counting how many customers
            # are already satisfied, and removing them
            # from the customer list.
            already_satisfied = 0
            for i in range(len(grumpy)):
                if grumpy[i] == 0: #He's happy
                    already_satisfied += customers[i]
                    customers[i] = 0
            
            # Part 2 requires finding the optinal number
            # of unhappy customers we can make happy.
            best_we_can_make_satisfied = 0
            current_satisfied = 0
            for i, customers_at_time in enumerate(customers):
                current_satisfied += customers_at_time # Add current to rolling total
                if i >= X: # We need to remove some from the rolling total
                    current_satisfied -= customers[i - X]
                best_we_can_make_satisfied = max(best_we_can_make_satisfied, current_satisfied)
            
            # The answer is the sum of the solutions for the 2 parts.
            return already_satisfied + best_we_can_make_satisfied
    



