---
layout: post
title: 553. Optimal Division
---
### Question
Given a list of **positive integers** , the adjacent integers will perform the
float division. For example, [2,3,4] -> 2 / 3 / 4.

However, you can add any number of parenthesis at any position to change the
priority of operations. You should find out how to add parenthesis to get the
**maximum** result, and return the corresponding expression in string format.
**Your expression should NOT contain redundant parenthesis.**

 **Example:**  

    
    
     **Input:** [1000,100,10,2]
    **Output:** "1000/(100/10/2)"
    **Explanation:**
    1000/(100/10/2) = 1000/((100/10)/2) = 200
    However, the bold parenthesis in "1000/( **(** 100/10 **)** /2)" are redundant,   
    since they don't influence the operation priority. So you should return "1000/(100/10/2)". 
    
    Other cases:
    1000/(100/10)/2 = 50
    1000/(100/(10/2)) = 50
    1000/100/10/2 = 0.5
    1000/100/(10/2) = 2
    

**Note:**

  1. The length of the input array is [1, 10].
  2. Elements in the given array will be in range [2, 1000].
  3. There is only one optimal division for each test case.

### Solution 1
X1/X2/X3/../Xn will always be equal to (X1/X2) * Y, no matter how you place
parentheses. i.e no matter how you place parentheses, X1 always goes to the
numerator and X2 always goes to the denominator. Hence you just need to
maximize Y. And Y is maximized when it is equal to X3 *..*Xn. So the answer is
always X1/(X2/X3/../Xn) = (X1 *X3 *..*Xn)/X2

    
    
    class Solution {
    public:
        string optimalDivision(vector<int>& nums) {
            string ans;
            if(!nums.size()) return ans;
            ans = to_string(nums[0]);
            if(nums.size()==1) return ans;
            if(nums.size()==2) return ans + "/" + to_string(nums[1]);
            ans += "/(" + to_string(nums[1]);
            for(int i = 2; i < nums.size();++i)
                ans += "/" + to_string(nums[i]);
            ans += ")";
            return ans;
    };
    


### Solution 2
    
    
    public class Solution {
        class Result {
            String str;
            double val;
        }
        
        public String optimalDivision(int[] nums) {
            int len = nums.length;
            return getMax(nums, 0, len - 1).str;
        }
        
        private Result getMax(int[] nums, int start, int end) {
            Result r = new Result();
            r.val = -1.0;
            
            if (start == end) {
                r.str = nums[start] + "";
                r.val = (double)nums[start];
            }
            else if (start + 1 == end) {
                r.str = nums[start] + "/" + nums[end];
                r.val = (double)nums[start] / (double)nums[end];
            }
            else {
                for (int i = start; i < end; i++) {
                    Result r1 = getMax(nums, start, i);
                    Result r2 = getMin(nums, i + 1, end);
                    if (r1.val / r2.val > r.val) {
                        r.str = r1.str + "/" + (end - i >= 2 ? "(" + r2.str + ")" : r2.str);
                        r.val = r1.val / r2.val;
                    }
                }
            }
            
            //System.out.println("getMax " + start + " " + end + "->" + r.str + ":" + r.val);
            return r;
        }
        
        private Result getMin(int[] nums, int start, int end) {
            Result r = new Result();
            r.val = Double.MAX_VALUE;
            
            if (start == end) {
                r.str = nums[start] + "";
                r.val = (double)nums[start];
            }
            else if (start + 1 == end) {
                r.str = nums[start] + "/" + nums[end];
                r.val = (double)nums[start] / (double)nums[end];
            }
            else {
                for (int i = start; i < end; i++) {
                    Result r1 = getMin(nums, start, i);
                    Result r2 = getMax(nums, i + 1, end);
                    if (r1.val / r2.val < r.val) {
                        r.str = r1.str + "/" + (end - i >= 2 ? "(" + r2.str + ")" : r2.str);
                        r.val = r1.val / r2.val;
                    }
                }
            }
            
            //System.out.println("getMin " + start + " " + end + "->" + r.str + ":" + r.val);
            return r;
        }
    }
    


### Solution 3
This is an Amazon interview question that could be solved in a very tricky
way. Since a lot of people have already posted their solutions, I just post a
brute force solution in case of the interviewer want a normal way.

I use recursion. For each recursion, I find the maximum result and also the
minimum result. For example, if you want to know the maximum result of A/B,
where A and B are also some expressions, then you only need to know the
maximum result of A and the minimum result of B. However, if you want to know
the maximum result of C/(A/B), then you also need to know the minimum result
of A/B. That's why both maximum and minimum should be stored.

    
    
    // by fallcreek
    public class Solution {
    
        public String optimalDivision(int[] nums) {
            Map<String, pair> memory = new HashMap<>();
            pair sol = divid(nums,0,nums.length-1, memory);
            return sol.maxS;
        }
        
        public pair divid(int[] nums, int start, int end, Map<String, pair> memory){
            String key = start + " " + end;
            if(memory.containsKey(key)) return memory.get(key);
            if(start == end)    return new pair(nums[start], "" + nums[start],nums[start], "" + nums[start]);
            
            pair sol = new pair(0,"",0,"");
            
            for(int i = start; i < end; i++){
                pair left = divid(nums, start, i, memory);
                pair right = divid(nums, i + 1, end, memory);
                
                double min = left.min / right.max;
                String minS = left.minS + "/" + (i + 1 == end ? right.maxS : "(" + right.maxS + ")"); 
                double max = left.max / right.min;
                String maxS = left.maxS + "/" + (i + 1 == end ? right.minS : "(" + right.minS + ")"); 
                if(sol.min == 0 || min < sol.min){
                    sol.min = min;
                    sol.minS = minS;
                }
                if(max > sol.max){
                    sol.max = max;
                    sol.maxS = maxS;
                }
            }
            memory.put(key, sol);
            return sol;
        }
    }
    
    class pair{
        double min;
        String minS;
        double max;
        String maxS;
        
        public pair(double min, String minS, double max, String maxS){
            this.min = min;
            this.minS = minS;
            this.max = max;
            this.maxS = maxS;
        }
    }
    



