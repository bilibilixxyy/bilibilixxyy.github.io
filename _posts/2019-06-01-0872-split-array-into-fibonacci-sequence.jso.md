---
layout: post
title: 872. Split Array into Fibonacci Sequence
---
### Question
Given a string `S` of digits, such as `S = "123456579"`, we can split it into
a _Fibonacci-like sequence_  `[123, 456, 579].`

Formally, a Fibonacci-like sequence is a list `F` of non-negative integers
such that:

  * `0 <= F[i] <= 2^31 - 1`, (that is, each integer fits a 32-bit signed integer type);
  * `F.length >= 3`;
  * and` F[i] + F[i+1] = F[i+2] `for all `0 <= i < F.length - 2`.

Also, note that when splitting the string into pieces, each piece must not
have extra leading zeroes, except if the piece is the number 0 itself.

Return any Fibonacci-like sequence split from `S`, or return `[]` if it cannot
be done.

 **Example 1:**

    
    
     **Input:** "123456579"
    **Output:** [123,456,579]
    

**Example 2:**

    
    
    **Input:** "11235813"
    **Output:** [1,1,2,3,5,8,13]
    

**Example 3:**

    
    
    **Input:** "112358130"
    **Output:** []
    **Explanation:** The task is impossible.
    

**Example 4:**

    
    
    **Input:** "0123"
    **Output:** []
    **Explanation:** Leading zeroes are not allowed, so "01", "2", "3" is not valid.
    

**Example 5:**

    
    
    **Input:** "1101111"
    **Output:** [110, 1, 111]
    **Explanation:** The output [11, 0, 11, 11] would also be accepted.
    

**Note:**

  1. `1 <= S.length <= 200`
  2. `S` contains only digits.

### Solution 1
    
    
    public List<Integer> splitIntoFibonacci(String S) {
        List<Integer> ans = new ArrayList<>();
        helper(S, ans, 0);
        return ans;
    }
    
    public boolean helper(String s, List<Integer> ans, int idx) {
        if (idx == s.length() && ans.size() >= 3) {
            return true;
        }
        for (int i=idx; i<s.length(); i++) {
            if (s.charAt(idx) == '0' && i > idx) {
                break;
            }
            long num = Long.parseLong(s.substring(idx, i+1));
            if (num > Integer.MAX_VALUE) {
                break;
            }
            int size = ans.size();
            // early termination
            if (size >= 2 && num > ans.get(size-1)+ans.get(size-2)) {
                break;
            }
            if (size <= 1 || num == ans.get(size-1)+ans.get(size-2)) {
                ans.add((int)num);
                // branch pruning. if one branch has found fib seq, return true to upper call
                if (helper(s, ans, i+1)) {
                    return true;
                }
                ans.remove(ans.size()-1);
            }
        }
        return false;
    }


### Solution 2
    
    
    class Solution {
    public:
    
        bool backtrack(string &S, int start, vector<int> &nums){        
            int n = S.size();
    	// If we reached end of string & we have more than 2 elements
    	// in our sequence then return true
            if(start >= n && nums.size()>=3){
                return true;
            }
    	// Since '0' in beginning is not allowed therefore if the current char is '0'
    	// then we can use it alone only and cannot extend it by adding more chars at the back.
    	// Otherwise we make take upto 10 chars (length of INT_MAX)
            int maxSplitSize = (S[start]=='0') ? 1 : 10;
    	// Try getting a solution by forming a number with 'i' chars begginning with 'start'
            for(int i=1; i<=maxSplitSize && start+i<=S.size(); i++){
                long long num = stoll(S.substr(start, i));
                if(num > INT_MAX)
                    return false;
                int sz = nums.size();
    	// If fibonacci property is not satisfied then we cannot get a solution
                if(sz >= 2 && nums[sz-1]+nums[sz-2] < num)
                    return false;
                if(sz<=1 || nums[sz-1]+nums[sz-2]==num){
                    nums.push_back(num);
                    if(backtrack(S, start+i, nums))
                        return true;
                    nums.pop_back();                
                }
            }
            return false;
        }
        
        vector<int> splitIntoFibonacci(string S) {
            vector<int> nums;
    	// Backtrack from 0th char
            backtrack(S, 0, nums);
            return nums;
        }
    };
    


### Solution 3
    
    
    class Solution(object):
        def splitIntoFibonacci(self, S):
            """
            :type S: str
            :rtype: List[int]
            """
            n = len(S)
            for i in xrange(1, 11):
                for j in xrange(1, 11):
                    if i + j >= n:
                        break
                    L = self.buildFibo(S, i, j)
                    if L:
                        return L
            return []
        
        def buildFibo(self, s, i, j):
            a = s[:i]
            b = s[i:i+j]
            if a[0] == '0' and i > 1:
                return []
            if b[0] == '0' and j > 1:
                return []
            
            offset = i + j
            n = len(s)
            x, y = int(a), int(b)
            arr = [x, y]
            while offset < n:
                z = x + y
                if z > 2147483647:
                    return []
                
                c = str(z)
                k = len(c)
                if offset + k > n or s[offset:offset+k] != c:
                    return []
                offset += k
                arr.append(z)
                x, y = y, z
            return arr
    



