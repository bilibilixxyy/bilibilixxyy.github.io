---
layout: post
title: 953. Reverse Only Letters
---
### Question
Given a string `S`, return the "reversed" string where all characters that are
not a letter stay in the same place, and all letters reverse their positions.



 **Example 1:**

    
    
     **Input:** "ab-cd"
    **Output:** "dc-ba"
    

**Example 2:**

    
    
    **Input:** "a-bC-dEf-ghIj"
    **Output:** "j-Ih-gfE-dCba"
    

**Example 3:**

    
    
    **Input:** "Test1ng-Leet=code-Q!"
    **Output:** "Qedo1ct-eeLg=ntse-T!"
    



 **Note:**

  1. `S.length <= 100`
  2. `33 <= S[i].ASCIIcode <= 122` 
  3. `S` doesn't contain `\` or `"`

### Solution 1
#  **Explanation**

Two pointers,  
from the begin and the of the string.  
find two letters and swap them.  
  

#  **Time Complexity** :

Time `O(N)`  
  

# Use while clause inside while loop

 **Java:**

    
    
         public String reverseOnlyLetters(String S) {
            StringBuilder sb = new StringBuilder(S);
            for (int i = 0, j = S.length() - 1; i < j;) {
                if (!Character.isLetter(sb.charAt(i))) {
                    ++i;
                } else if (!Character.isLetter(sb.charAt(j))) {
                    --j;
                } else {
                    sb.setCharAt(i, S.charAt(j));
                    sb.setCharAt(j--, S.charAt(i++));
                }
            }
            return sb.toString();
        }
    

**C++:**

    
    
         string reverseOnlyLetters(string S) {
            for (int i = 0, j = S.length() - 1; i < j;) {
                if (!isalpha(S[i]))
                    ++i;
                else if (!isalpha(S[j]))
                    --j;
                else
                    swap(S[i++], S[j--]);
            }
            return S;
        }
    

**Python:**

    
    
         def reverseOnlyLetters(self, S):
            S, i, j = list(S), 0, len(S) - 1
            while i < j:
                if not S[i].isalpha():
                    i += 1
                elif not S[j].isalpha():
                    j -= 1
                else:
                    S[i], S[j] = S[j], S[i]
                    i, j = i + 1, j - 1
            return "".join(S)
    

  

# Use if clause inside while loop

**C++:**

    
    
         string reverseOnlyLetters(string S) {
            for (int i = 0, j = S.length() - 1; i < j; ++i, --j) {
                while (i < j && !isalpha(S[i])) ++i;
                while (i < j && !isalpha(S[j])) --j;
                swap(S[i], S[j]);
            }
            return S;
        }
    

**Java:**

    
    
         public String reverseOnlyLetters(String S) {
            StringBuilder sb = new StringBuilder(S);
            for (int i = 0, j = S.length() - 1; i < j; ++i, --j) {
                while (i < j && !Character.isLetter(sb.charAt(i))) ++i;
                while (i < j && !Character.isLetter(sb.charAt(j))) --j;
                sb.setCharAt(i, S.charAt(j));
                sb.setCharAt(j, S.charAt(i));
            }
            return sb.toString();
        }
    

**Python:**

    
    
         def reverseOnlyLetters(self, S):
            i, j = 0, len(S) - 1
            S = list(S)
            while i < j:
                while i < j and not S[i].isalpha(): i += 1
                while i < j and not S[j].isalpha(): j -= 1
                S[i], S[j] = S[j], S[i]
                i, j = i + 1, j - 1
            return "".join(S)
    


### Solution 2
  * Idea is very simple.
  * Go over the string and construct new string:
    * by adding non-alphabetic characters or
    * dumping from "to be reversed" r stack.

    
    
    class Solution:
        def reverseOnlyLetters(self, S):
            r = [s for s in S if s.isalpha()]
            return "".join(S[i] if not S[i].isalpha() else r.pop() for i in range(len(S)))
    


### Solution 3
    
    
       string reverseOnlyLetters(string S) {
            int i = 0;
            int j = S.length()-1;
            
            while(i < j ){
                //if S[i] not a letter, increment to next
                if(!isalpha(S[i])) {
                    i++;
                    continue;
                }
                //if S[j] not a letter, decrement to next
                if(!isalpha(S[j])) {
                    j--;
                    continue;
                }
                
                //both are letters, swap & move i & j
                swap(S[i],S[j]);
                i++;
                j--;
                
            }       
            return S;
        }
    



