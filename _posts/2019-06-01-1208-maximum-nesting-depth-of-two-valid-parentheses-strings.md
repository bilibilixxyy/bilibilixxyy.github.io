---
layout: post
title: 1208. Maximum Nesting Depth of Two Valid Parentheses Strings
---
### Question
A string is a _valid parentheses string_  (denoted VPS) if and only if it
consists of `"("` and `")"` characters only, and:

  * It is the empty string, or
  * It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are VPS's, or
  * It can be written as `(A)`, where `A` is a VPS.

We can similarly define the _nesting depth_ `depth(S)` of any VPS `S` as
follows:

  * `depth("") = 0`
  * `depth(A + B) = max(depth(A), depth(B))`, where `A` and `B` are VPS's
  * `depth("(" + A + ")") = 1 + depth(A)`, where `A` is a VPS.

For example,  `""`, `"()()"`, and `"()(()())"` are VPS's (with nesting depths
0, 1, and 2), and `")("` and `"(()"` are not VPS's.



Given a VPS seq, split it into two disjoint subsequences `A` and `B`, such
that `A` and `B` are VPS's (and `A.length + B.length = seq.length`).

Now choose **any** such `A` and `B` such that `max(depth(A), depth(B))` is the
minimum possible value.

Return an `answer` array (of length `seq.length`) that encodes such a choice
of `A` and `B`:  `answer[i] = 0` if `seq[i]` is part of `A`, else `answer[i] =
1`.  Note that even though multiple answers may exist, you may return any of
them.



 **Example 1:**

    
    
     **Input:** seq = "(()())"
    **Output:** [0,1,1,1,1,0]
    

**Example 2:**

    
    
    **Input:** seq = "()(())()"
    **Output:** [0,0,0,1,1,0,1,1]
    



 **Constraints:**

  * `1 <= seq.size <= 10000`

### Solution 1
[Youtube](https://www.youtube.com/watch?v=_adniHPvQyE)  
[Bilibili](https://www.bilibili.com/video/av58142681)

## Solution 0: Alternatively Distribute Parentheses

Basically, `(` is 1 point, `)` is `-1` point.  
We try to keep total points of two groups even,  
by distributing parentheses alternatively.

The good part of this solution is that,  
we actually need no extra variable to record anything.

 **Java:**

    
    
         public int[] maxDepthAfterSplit(String seq) {
            int n = seq.length(), res[] = new int[n];
            for (int i = 0; i < n; ++i)
                res[i] = seq.charAt(i) == '(' ? i & 1 : (1 - i & 1);
            return res;
        }
    

**C++:**

    
    
         vector<int> maxDepthAfterSplit(string seq) {
            vector<int> res(seq.length());
            for (int i = 0; i < seq.length(); ++i)
                res[i] = i & 1 ^ (seq[i] == '(');
            return res;
        }
    

**1-line Python:**

    
    
         def maxDepthAfterSplit(self, seq):
            return [i & 1 ^ (seq[i] == '(') for i, c in enumerate(seq)]
    

  

# Complexity

Time `O(N)` for one pass  
Space `O(N)` for output  
  

# More

Also provide some more easy understood ideas for this problem,  
pick the the one you like.  
(As I keep receiving complaints about the readability,  
like no parentheses in solution for problem of parentheses)  
  

# Solution 1: Keep Two Group Even

Count the number of open parentheses of group `A` and group `B`.  
**Java:**

    
    
         public int[] maxDepthAfterSplit(String seq) {
            int A = 0, B = 0, n = seq.length();
            int[] res = new int[n];
            for (int i = 0; i < n; ++i) {
                if (seq.charAt(i) == '(') {
                    if (A < B) {
                        ++A;
                    } else {
                        ++B;
                        res[i] = 1;
                    }
                } else {
                    if (A > B) {
                        --A;
                    } else {
                        --B;
                        res[i] = 1;
                    }
                }
            }
            return res;
        }
    

**C++:**

    
    
         vector<int> maxDepthAfterSplit(string seq) {
            int A = 0, B = 0, n = seq.length();
            vector<int> res(n, 0);
            for (int i = 0; i < n; ++i) {
                if (seq[i] == '(') {
                    if (A < B) ++A;
                    else ++B, res[i] = 1;
                } else {
                    if (A > B) --A;
                    else --B, res[i] = 1;
                }
            }
            return res;
        }
    

**Python:**

    
    
         def maxDepthAfterSplit(self, seq):
            A = B = 0
            res = [0] * len(seq)
            for i, c in enumerate(seq):
                v = 1 if c == '(' else -1
                if (v > 0) == (A < B):
                    A += v
                else:
                    B += v
                    res[i] = 1
            return res
    
    

  

# Solution 2: Split by Half

Count the number of level of whole string.  
Then split it by half.  
Group 0: the part under the half height  
Group 1: the part above the half height

**Java:**

    
    
         public int[] maxDepthAfterSplit(String seq) {
            int depth = 0, cur = 0, n = seq.length();
            for (int i = 0; i < n; ++i) {
                cur +=  seq.charAt(i) == '(' ?  1 : -1;
                depth = Math.max(depth, cur);
            }
            int[] res = new int[n];
            for (int i = 0; i < n; ++i) {
                if (seq.charAt(i) == '(') {
                    if (++cur > depth / 2)
                        res[i] = 1;
                } else {
                    if (cur-- > depth / 2)
                        res[i] = 1;
                }
            }
            return res;
        }
    

**C++:**

    
    
         vector<int> maxDepthAfterSplit(string seq) {
            int depth = 0, cur = 0, n = seq.length();
            for (char c : seq)
                depth = max(depth, cur += c == '(' ? 1 : -1);
            vector<int> res(n, 0);
            for (int i = 0; i < n; ++i) {
                if (seq[i] == '(' && ++cur > depth / 2) res[i] = 1;
                if (seq[i] == ')' && cur-- > depth / 2) res[i] = 1;
            }
            return res;
        }
    

**Python:**

    
    
         def maxDepthAfterSplit(self, seq):
            depth = cur = 0
            for c in seq:
                if c == '(':
                    cur += 1
                    depth = max(depth, cur)
                else:
                    cur -= 1
            half = depth / 2
            res = [0] * len(seq)
            for i, c in enumerate(seq):
                if c == '(':
                    cur += 1
                    if cur > half: res[i] = 1
                else:
                    if cur > half: res[i] = 1
                    cur -= 1
            return res
    


### Solution 2
Just shave off all sub-VPSs of `level > max_level / 2` to 1 and appoint the
rest to 0.  
This may be visualized with a mountain that corresponds to a VPS.

The visualiztion method:

    
    
          _
    ( =  /     (Uphill)
    
         _
    ) =   \    (Downhill)
    

`()(())() will give the following hill`

    
    
    ( ) ( ( ) ) ( )
           __
     __  _/  \_  __  ____________________<] FIRE LASER     
    /  \/      \/  \                      | & SHAVE THE MOUNTAIN!
    
                         ||
                        \||/
                    	 \/
    
           __
          /  \           Top of mountain  = "   ()   "       
          
     __  ______  __
    /  \/      \/  \     Base of mountain = "()(  )()"
    
    

PR0GR4M C0D3:

    
    
    class Solution {
    public:
        vector<int> maxDepthAfterSplit(string seq) {
            int lvl = 0, maxlvl = 0;
            for (char c : seq) {
                if (c == '(') {
                    lvl++;
                    maxlvl = max(lvl, maxlvl);
                } else if (c == ')') {
                    lvl--;
                }
            }
            int cnt1 = 0;
            vector<int> ret(seq.size());
            vector<int> openings;
            int pos = 0;
            lvl = 0;
            for (char c : seq) {
                if (c == '(') {
                    lvl ++;
                    openings.push_back(pos);
                } else if (c == ')') {
                    bool should_make_1 = false;
                    
                    if (pos == seq.size()-1 && cnt1 == 0) {
                        should_make_1 = true;
                    }
                    if (/*lvl > maxlvl/2*/ lvl == maxlvl/2 + 1) {
                        should_make_1 = true;
                    }
                    
                    if (should_make_1) {
                        for (int i=openings.back(); i<=pos; i++) {
                            if (ret[i] == 0) cnt1 ++;
                            ret[i] = 1;
                        }
                    }
                    openings.pop_back();
                    lvl --;
                }
                
                pos ++;
            }
            return ret;
        }
    };
    


### Solution 3
Edit: This was fixed, this doesn't work anymore.

If you submit a vector with all 1's, it works.

The code below for C++ actually works.

class Solution {  
public:  
vector maxDepthAfterSplit(string seq) {  
vector answer(seq.length());  
for(int i=0;i<seq.length();i++) answer[i]=1;  
return answer;  
}  
};



