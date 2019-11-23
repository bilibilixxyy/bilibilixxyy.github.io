---
layout: post
title: 330. Patching Array
---
### Question
Given a sorted positive integer array _nums_ and an integer _n_ , add/patch
elements to the array such that any number in range `[1, n]` inclusive can be
formed by the sum of some elements in the array. Return the minimum number of
patches required.

 **Example 1:**

    
    
     **Input:** _nums_ = [1,3], _n_ = 6
    **Output:** 1 
    **Explanation:**
    Combinations of _nums_ are [1], [3], [1,3], which form possible sums of: 1, 3, 4.
    Now if we add/patch 2 to _nums_ , the combinations are: [1], [2], [3], [1,3], [2,3], [1,2,3].
    Possible sums are 1, 2, 3, 4, 5, 6, which now covers the range [1, 6].
    So we only need 1 patch.

**Example 2:**

    
    
    **Input:** _nums_ = [1,5,10], _n_ = 20
    **Output:** 2
    **Explanation:** The two patches can be [2, 4].
    

**Example 3:**

    
    
    **Input:** _nums_ = [1,2,2], _n_ = 5
    **Output:** 0
    

### Solution 1
 **Solution**

    
    
    int minPatches( vector<int>& nums, int n) {
        long miss = 1, added = 0, i = 0;
        while (miss <= n) {
            if (i < nums.size() && nums[i] <= miss) {
                miss += nums[i++];
            } else {
                miss += miss;
                added++;
            }
        }
        return added;
    }
    

* * *

**Explanation**

Let `miss` be the smallest sum in `[0,n]` that we might be missing. Meaning we
already know we can build all sums in `[0,miss)`. Then if we have a number
`num <= miss` in the given array, we can add it to those smaller sums to build
all sums in `[0,miss+num)`. If we don't, then we must add such a number to the
array, and it's best to add `miss` itself, to maximize the reach.

* * *

**Example:** Let's say the input is `nums = [1, 2, 4, 13, 43]` and `n = 100`.
We need to ensure that all sums in the range [1,100] are possible.

Using the given numbers 1, 2 and 4, we can already build all sums from 0 to 7,
i.e., the range [0,8). But we can't build the sum 8, and the next given number
(13) is too large. So we insert 8 into the array. Then we can build all sums
in [0,16).

Do we need to insert 16 into the array? No! We can already build the sum 3,
and adding the given 13 gives us sum 16. We can also add the 13 to the other
sums, extending our range to [0,29).

And so on. The given 43 is too large to help with sum 29, so we must insert 29
into our array. This extends our range to [0,58). But then the 43 becomes
useful and expands our range to [0,101). At which point we're done.

* * *

**Another implementation** , though I prefer the above one.

    
    
    int minPatches(vector<int>& nums, int n) {
        int count = 0, i = 0;
        for (long miss=1; miss <= n; count++)
            miss += (i < nums.size() && nums[i] <= miss) ? nums[i++] : miss;
        return count - i;
    }


### Solution 2
The question asked for the " **minimum** number of patches required". In other
words, it asked for an optimal solution. Lots of problems involving optimal
solution can be solved by dynamic programming and/or greedy algorithm. I
started with greedy algorithm which is conceptually easy to design. Typically,
a greedy algorithm needs selection of best moves for a subproblem. So what is
our best move?

Think about this example: nums = [1, 2, 3, 9]. We naturally want to iterate
through nums from left to right and see what we would discover. After we
encountered 1, we know 1...1 is patched completely. After encountered 2, we
know 1...3 (1+2) is patched completely. After we encountered 3, we know 1...6
(1+2+3) is patched completely. After we encountered 9, the smallest number we
can get is 9. So we must patch a new number here so that we don't miss 7, 8.
To have 7, the numbers we can patch is 1, 2, 3 ... 7. Any number greater than
7 won't help here. Patching 8 will not help you get 7. So we have 7 numbers
(1...7) to choose from. I hope you can see number 7 works best here because if
we chose number 7, we can move all the way up to 1+2+3+7 = 13. (1...13 is
patched completely) and it makes us reach n as quickly as possible. After we
patched 7 and reach 13, we can consider last element 9 in nums. Having 9 makes
us reach 13+9 = 22, which means 1...22 is completely patched. If we still
did't reach n, we can then patch 23, which makes 1...45 (22+23) completely
patched. We continue until we reach n.


### Solution 3
show the algorithm with an example,

let nums=[1 2 5 6 20], n = 50.

Initial value: with 0 nums, we can only get 0 maximumly.

Then we need to get 1, since nums[0]=1, then we can get 1 using [1]. now the
maximum number we can get is 1. (actually, we can get all number no greater
than the maximum number)

    
    
    number used [1], number added []
    can achieve 1~1
    

Then we need to get 2 (maximum number +1). Since nums[1]=2, we can get 2. Now
we can get all number between 1 ~ 3 (3=previous maximum value + the new number
2). and 3 is current maximum number we can get.

    
    
    number used [1 2], number added []
    can achieve 1~3
    

Then we need to get 4 (3+1). Since nums[2]=5>4; we need to add a new number to
get 4. The optimal solution is to add 4 directly. In this case, we could
achieve maximumly 7, using [1,2,4].

    
    
    number used [1 2], number added [4]
    can achieve 1~7
    

Then we need to get 8 (7+1). Since nums[2]=5<8, we can first try to use 5. Now
the maximum number we can get is 7+5=12. Since 12>8, we successfully get 8.

    
    
    number used [1 2 5], number added [4]
    can achieve 1~12
    

Then we need to get 13 (12+1), Since nums[3]=6<13, we can first try to use 6.
Now the maximum number we can get is 12+6=18. Since 18>13, we successfully get
13.

    
    
    number used [1 2 5 6], number added [4]
    can achieve 1~18
    

Then we need to get 19 (18+1), Since nums[4]=20>19, we need to add a new
number to get 19. The optimal solution is to add 19 directly. In this case, we
could achieve maximumly 37.

    
    
    number used [1 2 5 6], number added [4 19]
    can achieve 1~37
    

Then we need to get 38(37+1), Since nums[4]=20<38, we can first try to use 20.
Now the maximum number we can get is 37+20=57. Since 57>38, we successfully
get 38.

    
    
    number used [1 2 5 6 20], number added [4 19]
    can achieve 1~57
    

Since 57>n=50, we can all number no greater than 50.

The extra number we added are 4 and 19, so we return 2.

The code is given as follows

    
    
    class Solution {
    public:
    int minPatches(vector<int>& nums, int n) {
        int cnt=0,i=0;
        long long maxNum=0;
        while (maxNum<n){
           if (i<nums.size() && nums[i]<=maxNum+1)
                maxNum+=nums[i++];
           else{
                maxNum+=maxNum+1;cnt++;
           }
       }
       return cnt;
    }
    };



