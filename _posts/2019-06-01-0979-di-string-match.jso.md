---
layout: post
title: 979. DI String Match
---
### Question
Given a string `S` that **only** contains "I" (increase) or "D" (decrease),
let `N = S.length`.

Return **any** permutation `A` of `[0, 1, ..., N]` such that for all `i = 0,
..., N-1`:

  * If `S[i] == "I"`, then `A[i] < A[i+1]`
  * If `S[i] == "D"`, then `A[i] > A[i+1]`



 **Example 1:**

    
    
     **Input:** "IDID"
    **Output:** [0,4,1,3,2]
    

**Example 2:**

    
    
    **Input:** "III"
    **Output:** [0,1,2,3]
    

**Example 3:**

    
    
    **Input:** "DDI"
    **Output:** [3,2,0,1]



 **Note:**

  1. `1 <= S.length <= 10000`
  2. `S` only contains characters `"I"` or `"D"`.

### Solution 1
 **Java, Outside-in:**

    
    
        public  int[] diStringMatch(String S) {
            int n = S.length(), left = 0, right = n;
            int[] res = new int[n + 1];
            for (int i = 0; i < n; ++i)
                res[i] = S.charAt(i) == 'I' ? left++ : right--;
            res[n] = left;
            return res;
        }
    

**C++, Inside-out:**

    
    
        vector< int> diStringMatch(string S) {
            int left = count(S.begin(), S.end(), 'D'), right = left;
            vector<int> res = {left};
            for (char &c : S)
                res.push_back(c == 'I' ? ++right : --left);
            return res;
        }
    

**Python, Inside-out and subtract left:**

    
    
        def diStringMatch(self, S):
             left = right = 0
            res = [0]
            for i in S:
                if i == "I":
                    right += 1
                    res.append(right)
                else:
                    left -= 1
                    res.append(left)
            return [i - left for i in res]
    


### Solution 2
We track high (`h = N - 1`) and low (`l = 0`) numbers within `[0 ... N - 1]`.
When we encounter 'I', we insert the current low number and increase it. With
'D', we insert the current high number and decrease it. In the end, `h == l`,
so we insert that last number to complete the premutation.

    
    
    vector<int> diStringMatch(string S) {
        vector<int> res;
        for (int l = 0, h = S.size(), i = 0; i <= S.size(); ++i)
            res.push_back(i == S.size() || S[i] == 'I' ? l++ : h--);
        return res;
    }
    


### Solution 3
  * Think of available numbers to put as an array of [0, 1, 2, ..., len(S)]
  * When you see "I", your safest option is to put lowest(leftmost) number for the next move, so it will always increase
  * When you see "D", your safest option is to put highest(rightmost) number for the next move, so it will always decrease
  * Don't forget to put latest number when l == r

    
    
    class Solution:
        def diStringMatch(self, S):
            l, r, arr = 0, len(S), []
            for s in S:
                arr.append(l if s == "I" else r)
                l, r = l + (s == "I"), r - (s == "D")
            return arr + [l]
    



