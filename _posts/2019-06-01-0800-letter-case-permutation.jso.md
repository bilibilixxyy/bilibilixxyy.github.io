---
layout: post
title: 800. Letter Case Permutation
---
### Question
Given a string S, we can transform every letter individually to be lowercase
or uppercase to create another string.  Return a list of all possible strings
we could create.

    
    
     **Examples:**
    **Input:** S = "a1b2"
    **Output:** ["a1b2", "a1B2", "A1b2", "A1B2"]
    
    **Input:** S = "3z4"
    **Output:** ["3z4", "3Z4"]
    
    **Input:** S = "12345"
    **Output:** ["12345"]
    

**Note:**

  * `S` will be a string with length between `1` and `12`.
  * `S` will consist only of letters or digits.

### Solution 1
When I saw a problem, my first step is to draw a figure. See the figure below:  
`abc`  
`abc Abc` 0  
`abc aBc Abc ABc` 1  
`abc abC aBc aBC Abc AbC ABc ABC` 2

There we go! Is that a typical BFS/DFS problem? Yes, you are right!  
Be careful to check whether a character is a digit or a letter(lower case or
upper case).  
`BFS Solution:`

    
    
    class Solution {
        public List<String> letterCasePermutation(String S) {
            if (S == null) {
                return new LinkedList<>();
            }
            Queue<String> queue = new LinkedList<>();
            queue.offer(S);
            
            for (int i = 0; i < S.length(); i++) {
                if (Character.isDigit(S.charAt(i))) continue;            
                int size = queue.size();
                for (int j = 0; j < size; j++) {
                    String cur = queue.poll();
                    char[] chs = cur.toCharArray();
                    
                    chs[i] = Character.toUpperCase(chs[i]);
                    queue.offer(String.valueOf(chs));
                    
                    chs[i] = Character.toLowerCase(chs[i]);
                    queue.offer(String.valueOf(chs));
                }
            }
            
            return new LinkedList<>(queue);
        }
    }
    

`DFS Solution:`

    
    
    class Solution {
        public List<String> letterCasePermutation(String S) {
            if (S == null) {
                return new LinkedList<>();
            }
            
            List<String> res = new LinkedList<>();
            helper(S.toCharArray(), res, 0);
            return res;
        }
        
        public void helper(char[] chs, List<String> res, int pos) {
            if (pos == chs.length) {
                res.add(new String(chs));
                return;
            }
            if (chs[pos] >= '0' && chs[pos] <= '9') {
                helper(chs, res, pos + 1);
                return;
            }
            
            chs[pos] = Character.toLowerCase(chs[pos]);
            helper(chs, res, pos + 1);
            
            chs[pos] = Character.toUpperCase(chs[pos]);
            helper(chs, res, pos + 1);
        }
    }
    


### Solution 2
    
    
    def letterCasePermutation(self, S):
            res = ['']
            for ch in S:
                if ch.isalpha():
                    res = [i+j for i in res for j in [ch.upper(), ch.lower()]]
                else:
                    res = [i+ch for i in res]
            return res
    


### Solution 3
Straightforward recursive solution. Simple trick with case toggling.  
Thanks @shiowen for noticing redundant lines.

    
    
    class Solution {
        void backtrack(string &s, int i, vector<string> &res) {
            if (i == s.size()) {
                res.push_back(s);
                return;
            }
            backtrack(s, i + 1, res);
            if (isalpha(s[i])) {
                // toggle case
                s[i] ^= (1 << 5);
                backtrack(s, i + 1, res);
            }
        }
    public:
        vector<string> letterCasePermutation(string S) {
            vector<string> res;
            backtrack(S, 0, res);
            return res;
        }
    };
    



