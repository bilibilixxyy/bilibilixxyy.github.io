---
layout: post
title: 398. Random Pick Index
---
### Question
Given an array of integers with possible duplicates, randomly output the index
of a given target number. You can assume that the given target number must
exist in the array.

 **Note:**  
The array size can be very large. Solution that uses too much extra space will
not pass the judge.

 **Example:**

    
    
    int[] nums = new int[] {1,2,3,3,3};
    Solution solution = new Solution(nums);
    
    // pick(3) should return either index 2, 3, or 4 randomly. Each index should have equal probability of returning.
    solution.pick(3);
    
    // pick(1) should return 0. Since in the array only nums[0] is equal to 1.
    solution.pick(1);
    

### Solution 1
    
    
    public class Solution {
    
        int[] nums;
        Random rnd;
    
        public Solution(int[] nums) {
            this.nums = nums;
            this.rnd = new Random();
        }
        
        public int pick(int target) {
            int result = -1;
            int count = 0;
            for (int i = 0; i < nums.length; i++) {
                if (nums[i] != target)
                    continue;
                if (rnd.nextInt(++count) == 0)
                    result = i;
            }
            
            return result;
        }
    }```


### Solution 2
Because I've made a rather naive map-of-index-lists Java solution and it was
happily accepted by the OJ. So far I see three types of solutions:

  1. Like mine, O(N) memory, O(N) init, O(1) pick.

  2. Like @dettier's [Reservoir Sampling](https://discuss.leetcode.com/topic/58301/simple-reservoir-sampling-solution). O(1) init, O(1) memory, but O(N) to pick.

  3. Like @chin-heng's [binary search](https://discuss.leetcode.com/topic/58295/share-my-c-solution-o-lg-n-to-pick-o-nlg-n-for-sorting): O(N) memory, O(N lg N) init, O(lg N) pick.

Are all three kinds acceptable?


### Solution 3
Actually, we could first consider following question:

 **You have a file consisting of characters. The characters in the file can be
read sequentially, but the length of the file is unknown. How do you pick a
character so that every character in the file has equal probability of being
chosen?**

For this problem we can take algorithm like this:

  * Draw the 1st char. If there is a second char, then we will hold 1st char by prob = 1/2, and replace the 1st char to 2nd char with prob = 1/2. After this step we suppose that the char is X now.

  * After then, if there is 3rd char, then we will hold the X with prob = 2/3 and replace X to 3rd char with prob = 1/3. Why do they hold the same prob to be picked?  
Because:  
 **Obviously, Prob(the 3rd char is picked) = 1/3;**  
 **Prob(the 2nd char is picked) = 1 * 1/2 * 2/3 = 1/3;**  
 **Prob(the 1st char is picked) = 1 * 1/2 * 2/3 = 1/3;**

  * So we can say that when we now has n chars and there is still another char in the file, we can pick the other char with prob= 1/(n+1), also keep original char with prob = n/(n+1), then we can secure each char is picked with same prob = 1/(n+1), because **prob = 1 * 1/2 * 2/3 * ···· * n/(n+1) = 1/(n+1).**

Now, go back to this problem. The thought is the same, when we meet some
**nums[i] == target** , we can use above conclusion: **we can pick the other
char with prob= 1/(n+1), also keep original char with prob = n/(n+1), then we
can secure each char is picked with same prob = 1/(n+1).**

 **Code:**

    
    
     class Solution {
    vector<int> _nums;
    public:
    Solution(vector<int> nums) {
        _nums = nums;
    }
    
    int pick(int target) {
        int n = 0, ans = -1;
        for(int i = 0 ; i < _nums.size(); i++){
            if(_nums[i] != target) continue;
            if(n == 0){ans = i; n++;}
            else{
                n++;
                if(rand() % n == 0){ans = i;}// with prob 1/(n+1) to replace the previous index
            }
        }
        return ans;
    }
    };
    

![0_1475804025522_F32094D6-140C-47AB-
AD65-C4460D0C19AA.png](/uploads/files/1475804029350-f32094d6-140c-47ab-
ad65-c4460d0c19aa.png)



