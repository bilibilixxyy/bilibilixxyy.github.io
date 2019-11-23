---
layout: post
title: 1128. Remove All Adjacent Duplicates In String
---
### Question
Given a string `S` of lowercase letters, a _duplicate removal_ consists of
choosing two adjacent and equal letters, and removing  them.

We repeatedly make duplicate removals on S until we no longer can.

Return the final string after all such duplicate removals have been made.  It
is guaranteed the answer is unique.



 **Example 1:**

    
    
     **Input:** "abbaca"
    **Output:** "ca"
    **Explanation:**
    For example, in "abbaca" we could remove "bb" since the letters are adjacent and equal, and this is the only possible move.   The result of this move is that the string is "aaca", of which only "aa" is possible, so the final string is "ca".
    



 **Note:**

  1. `1 <= S.length <= 20000`
  2. `S` consists only of English lowercase letters.

### Solution 1
Keep a `res` as current result.  
Loop on characters in the string `S` one by one.  
If the next character is the same as the last in `res`, pop the last character
from `res`.  
Otherwise append the the next character to the end of `res`.

 **Java**

    
    
        public String remove Duplicates(String S) {
            int i = 0, n = S.length();
            char[] stack = new char[n];
            for (int j = 0; j < n; ++j)
                if (i > 0 && stack[i - 1] == S.charAt(j))
                    --i;
                else
                    stack[i++] = S.charAt(j);
            return new String(stack, 0, i);
        }
    

**C++**

    
    
         string removeDuplicates(string S) {
            string res = "";
            for (char& c : S)
                if (res.size() && c == res.back())
                    res.pop_back();
                else
                    res.push_back(c);
            return res;
        }
    

**Python**

    
    
        def removeDuplicates(self, S):
             res = []
            for c in S:
                if res and res[-1] == c:
                    res.pop()
                else:
                    res.append(c)
            return "".join(res)
    

**Python 1-line**

    
    
         def removeDuplicates(self, S):
            return reduce(lambda s, c: s[:-1] if s[-1] == c else s + c, S, "#")[1:]
    


### Solution 2
**Method 1: ArrayDeque**

If current char is same as previous char in the ArrayDeque, pop out the
previous char;  
Otherwise, add current char into the ArrayDeque.

    
    
        public String removeDuplicates(String S) {
            Deque<Character> dq = new ArrayDeque<>();
            for (char c : S.toCharArray()) {
                if (!dq.isEmpty() && dq.peekLast() == c) { 
                    dq.pollLast();
                }else {
                    dq.offer(c);
                }
            }
            StringBuilder sb = new StringBuilder();
            for (char c : dq) { sb.append(c); }
            return sb.toString();
        }
    

**Method 2: StringBuilder.**

Get rid of the ArrayDeque in method 1, use only StringBuilder.

If current char is same as the end of the StringBuilder, delete the char at
end; otherwise, append it at the end.

    
    
        public String removeDuplicates(String S) {
            StringBuilder sb = new StringBuilder();
            for (char c : S.toCharArray()) {
                int size = sb.length();
                if (size > 0 && sb.charAt(size - 1) == c) { 
                    sb.deleteCharAt(size - 1); 
                }else { 
                    sb.append(c); 
                }
            }
            return sb.toString();
        }
    

**Method 3: two pointers**

If current char is same as the end of non-adjacent-duplicate chars, decrease
the counter `end` by 1;  
otherwise, copy the current char to its end.

    
    
        public String removeDuplicates(String S) {
            char[] a = S.toCharArray();
            int end = -1;
            for (char c : a) {
                if (end >= 0 && a[end] == c) { 
                    --end; 
                }else { 
                    a[++end] = c; 
                }
            }
            return String.valueOf(a, 0, end + 1);
        }
    

**Analysis for all methods:**

Time & space: O(n), where n = S.length().


### Solution 3
    
    
    class Solution {
    public:
        string removeDuplicates(string S) {
            string a;
            for (auto& c : S) 
                if (a.size() && a.back() == c) a.pop_back();
                else a.push_back(c);
            return a;
        }
    };
    



