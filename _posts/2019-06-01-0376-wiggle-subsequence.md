---
layout: post
title: 376. Wiggle Subsequence
---
### Question
A sequence of numbers is called a **wiggle sequence** if the differences
between successive numbers strictly alternate between positive and negative.
The first difference (if one exists) may be either positive or negative. A
sequence with fewer than two elements is trivially a wiggle sequence.

For example, `[1,7,4,9,2,5]` is a wiggle sequence because the differences
`(6,-3,5,-7,3)` are alternately positive and negative. In contrast,
`[1,4,7,2,5]` and `[1,7,4,5,5]` are not wiggle sequences, the first because
its first two differences are positive and the second because its last
difference is zero.

Given a sequence of integers, return the length of the longest subsequence
that is a wiggle sequence. A subsequence is obtained by deleting some number
of elements (eventually, also zero) from the original sequence, leaving the
remaining elements in their original order.

 **Example 1:**

    
    
     **Input:** [1,7,4,9,2,5]
    **Output:** 6
    **Explanation:**  The entire sequence is a wiggle sequence.

**Example 2:**

    
    
    **Input:** [1,17,5,10,13,15,10,5,16,8]
    **Output:** 7
    **Explanation:** There are several subsequences that achieve this length. One is [1,17,10,13,10,16,8].

**Example 3:**

    
    
    **Input:** [1,2,3,4,5,6,7,8,9]
    **Output:** 2

 **Follow up:**  
Can you do it in O( _n_ ) time?

### Solution 1
For every position in the array, there are only three possible statuses for
it.

  * up position, it means nums[i] > nums[i-1]
  * down position, it means nums[i] < nums[i-1]
  * equals to position, nums[i] == nums[i-1]

So we can use two arrays up[] and down[] to record _the max wiggle sequence
length so far_ at index _**i**_.  
If nums[i] > nums[i-1], that means it wiggles up. the element before it must
be a down position. so up[i] = down[i-1] + 1; down[i] keeps the same with
before.  
If nums[i] < nums[i-1], that means it wiggles down. the element before it must
be a up position. so down[i] = up[i-1] + 1; up[i] keeps the same with before.  
If nums[i] == nums[i-1], that means it will not change anything becasue it
didn't wiggle at all. so both down[i] and up[i] keep the same.

In fact, we can reduce the space complexity to O(1), but current way is more
easy to understanding.

    
    
    public class Solution {
        public int wiggleMaxLength(int[] nums) {
            
            if( nums.length == 0 ) return 0;
            
            int[] up = new int[nums.length];
            int[] down = new int[nums.length];
            
            up[0] = 1;
            down[0] = 1;
            
            for(int i = 1 ; i < nums.length; i++){
                if( nums[i] > nums[i-1] ){
                    up[i] = down[i-1]+1;
                    down[i] = down[i-1];
                }else if( nums[i] < nums[i-1]){
                    down[i] = up[i-1]+1;
                    up[i] = up[i-1];
                }else{
                    down[i] = down[i-1];
                    up[i] = up[i-1];
                }
            }
            
            return Math.max(down[nums.length-1],up[nums.length-1]);
        }
    }
    


### Solution 2
In Wiggle Subsequence, think that the solution we need should be in a way that
we get alternative higher, lower,higher number.  
Eg: 2, 5, 3, 8, 6, 9  
In above example, the sequence of numbers is small,big,small,big,small,big
numbers (In shape of hill).

Now for explanation, we take example series:  
2,1,4,5,6,3,3,4,8,4

First we check if the series is starting as (big, small) or (small, big). So
as 2,1 is big, small. So we will start the loop as we need small number first
that is 1 as 2 is already there.

    
    
    Step 1: First we check our requirement is to get small number. As 1<2 so the series will be
     2,1
    
    
    
    Step 2: Now we need big number that is  greater than 1. As 4>1 so series  will be
    2,1,4
    
    
    
    Step 3: Now we need small number. But 5>4 so 4 will be replaced by 5. So the series will become
    2,1,5
    
    
    
    Step 4:  We need small number. But 6>5. Series will be
    2,1,6
    
    
    
    Step 5: Require small number. 3<6. Series will be
    2,1,6,3
    
    
    
    Step 6: Require big number. 3=3. No change in series
    2,1,6,3
    
    
    
    Step 7: Require big number. 4>3. Series will become
    2,1,6,3,4
    
    
    
    Step 8:  Require small number. 8>4. 8 will  replace 4 and series will become
    2,1,6,3,8
    
    
    
    Step 9: Require small number. 4<8. So final series will  be
    2,1,6,3,8,4
    

Answer is 6.

In the code, for constant space O(1) we will modify the same 'num' array to
store the (small, big, small) hill shape values. So the code will not only
calculate the length of the sequence but if the interviewers asks for the
Wiggle series also then we can return the series too. The leetcode Online
Judge skipped a test case if the series starts with same set of numbers.
Thanks to @ztq63830398, modified the code to consider that test case also.

Code:

    
    
        public class Solution {
    	public int wiggleMaxLength(int[] nums) {
    		if (nums.length == 0 || nums.length == 1) {
    			return nums.length;
    		}
    		int k = 0;
    		while (k < nums.length - 1 && nums[k] == nums[k + 1]) {  //Skips all the same numbers from series beginning eg 5, 5, 5, 1
    			k++;
    		}
    		if (k == nums.length - 1) {
    			return 1;
    		}
    		int result = 2;     // This will track the result of result array
    		boolean smallReq = nums[k] < nums[k + 1];       //To check series starting pattern
    		for (int i = k + 1; i < nums.length - 1; i++) {
    			if (smallReq && nums[i + 1] < nums[i]) {
    				nums[result] = nums[i + 1];
    				result++;
    				smallReq = !smallReq;    //Toggle the requirement from small to big number
    			} else {
    				if (!smallReq && nums[i + 1] > nums[i]) {
    					nums[result] = nums[i + 1];
    					result++;
    					smallReq = !smallReq;    //Toggle the requirement from big to small number
    				}
    			}
    		}
    		return result;
    	}
    }
    


### Solution 3
    
    
    class Solution {
    public:
        int wiggleMaxLength(vector<int>& nums) {
            int size = nums.size();
            
            if (size == 0) {
                return 0;
            }
            
            /** up[i] is the length of a longest wiggle subsequence of {nums[0],...,nums[i]}, with a
                positive difference between its last two numbers. This subsequence may or may not
                include nums[i] and there may be several such subsequences (of the same length).
                We call this a subsequence of type U.
             */
            vector<int> up(size, 0);
            /** down[i] is the length of a longest wiggle subsequence of {nums[0],...,nums[i]}, with a
                negative difference between its last two numbers. This subsequence may or may not
                include nums[i] and there may be several such subsequences (of the same length).
                We call this a subsequence of type D.
             */
            vector<int> down(size, 0);
            
            // At i=0, there is only one number and we can use it as a subsequence, i.e up[0]=down[0]=1
            up[0] = 1;
            down[0] = 1;
            for(int i=1; i<size; ++i){
                
                if (nums[i] > nums[i-1]) {
                    /** If nums[i] > nums[i-1], then we can use nums[i] to make a longer subsequence of type U
                        Proof: We consider a subsequence of type D in {0,...,i-1} (its length is down[i-1]).
                        Let N be the last number of this subsequence.
                        - If nums[i] > N, then we can add nums[i] to the subsequence and it gives us a longer
                        valid subsequence of type U.
                        - If nums[i] <= N, then:
                        (1) N cannot be nums[i-1], because nums[i-1] < nums[i] <= N i.e. nums[i-1] < N
                        (2) We can replace N with nums[i-1] (we still have a valid
                        subsequence of type D since N >= nums[i] > nums[i-1] i.e. N > nums[i-1]),
                        and then add nums[i] to the subsequence, and we have a longer subsequence of type U.
                        Therefore up[i] = down[i-1] + 1
                        
                        There is no gain in using nums[i] to make a longer subsequence of type D.
                        Proof: Let N be the last number of a subsequence of type U
                        in {0,...,i-1}.
                        Assume we can use nums[i] to make a longer subsequence of type D. Then:
                        (1) N cannot be nums[i-1], otherwise we would not be able to use nums[i]
                        to make a longer subsequence of type D as nums[i] > nums[i-1]
                        (2) Necessarily nums[i] < N, and therefore nums[i-1] < N since nums[i-1] < nums[i].
                        But this means that we could have used nums[i-1] already to make a longer
                        subsequence of type D.
                        So even if we can use nums[i], there is no gain in using it, so we keep the old value of
                        down (down[i] = down[i-1])
                    */
                    up[i] = down[i-1] + 1;
                    down[i] = down[i-1];
                }
                else if (nums[i] < nums[i-1]) {
                    /** The reasoning is similar if nums[i] < nums[i-1] */
                    down[i] = up[i-1] + 1;
                    up[i] = up[i-1];
                }
                else {
                    /** if nums[i] == nums[i-1], we cannot do anything more than what we did with
                         nums[i-1] so we just keep the old values of up and down
                    */
                    up[i] = up[i-1];
                    down[i] = down[i-1];
                }
            }
            return max(up[size-1], down[size-1]);
        }
    };
    



