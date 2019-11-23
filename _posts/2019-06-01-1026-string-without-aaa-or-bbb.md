---
layout: post
title: 1026. String Without AAA or BBB
---
### Question
Given two integers `A` and `B`, return **any** string `S` such that:

  * `S` has length `A + B` and contains exactly `A` `'a'` letters, and exactly `B` `'b'` letters;
  * The substring `'aaa'` does not occur in `S`;
  * The substring `'bbb'` does not occur in `S`.



 **Example 1:**

    
    
     **Input:** A = 1, B = 2
    **Output:** "abb"
    **Explanation:** "abb", "bab" and "bba" are all correct answers.
    

**Example 2:**

    
    
    **Input:** A = 4, B = 1
    **Output:** "aabaa"



 **Note:**

  1. `0 <= A <= 100`
  2. `0 <= B <= 100`
  3. It is guaranteed such an `S` exists for the given `A` and `B`.

### Solution 1
First, I must note that this problem does not feel like 'Easy'. The
implementation is easy, but the intuition is not. In fact, I spent more time
on this problem then on other 'Medium' problems in the contest.

  * If the initial number of As is greater than Bs, we swap A and B.
  * For each turn, we add 'a' to our string.
  * If the number of remaining As is greater than Bs, we add one more 'a'.
  * Finally, we add 'b'.

If you feel rather functional, here is a 3-liner (on par with the Python
solution, courtesy of [@zengxinhai](https://leetcode.com/zengxinhai)):


### Solution 2
C++:

    
    
    class Solution {
    public:
        string strWithout3a3b(int A, int B) {
            if(A == 0) return string(B, 'b');
            else if(B == 0) return string(A, 'a');
            else if(A == B) return "ab" + strWithout3a3b(A - 1, B - 1);
            else if(A > B) return "aab" + strWithout3a3b(A - 2, B - 1);
            else return strWithout3a3b(A - 1, B - 2) + "abb";
        }
    };
    

Python:

    
    
    class Solution(object):
        def strWithout3a3b(self, A, B):
            if A == 0:      
    			return 'b' * B
            elif B == 0:    
    			return 'a' * A
            elif A == B:    
    			return 'ab' * A
            elif A > B:     
    			return 'aab' + self.strWithout3a3b(A - 2, B - 1)
            else:           
    			return self.strWithout3a3b(A - 1, B - 2) + 'abb'
    


### Solution 3
  1. If current result ends with `aa`, next char is `b`; if ends with `bb`, next char must be `a`.
  2. Other cases, if A > B, next char is `a`; otherwise, next char is `b`.

    
    
        public String strWithout3a3b(int A, int B) {
            StringBuilder sb = new StringBuilder(A + B);
            while (A + B > 0) {
                String s = sb.toString();
                if (s.endsWith("aa")) {
                    sb.append("b");
                    --B;       
                }else if (s.endsWith("bb")){
                    sb.append("a");
                    --A;
                }else if (A > B) {
                    sb.append("a");
                    --A;
                }else {
                    sb.append("b");
                    --B;
                }
            }
            return sb.toString();
        }
    

Recursive Version, inspired by @lbjlc

    
    
        public String strWithout3a3b(int A, int B) {
            if (A == 0 || B == 0) { return String.join("", Collections.nCopies(A + B, A == 0 ? "b" : "a")); } // if A or B is 0, return A 'a's or B 'b's.
            if (A == B) { return "ab" + strWithout3a3b(A - 1, B - 1); }
            if (A > B) { return "aab" + strWithout3a3b(A - 2, B - 1); }
            return "bba" + strWithout3a3b(A - 1, B - 2);
        }
    



