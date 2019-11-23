---
layout: post
title: 874. Backspace String Compare
---
### Question
Given two strings `S` and `T`, return if they are equal when both are typed
into empty text editors. `#` means a backspace character.

 **Example 1:**

    
    
     **Input:** S = "ab#c", T = "ad#c"
    **Output:** true
    **Explanation** : Both S and T become "ac".
    

**Example 2:**

    
    
    **Input:** S = "ab##", T = "c#d#"
    **Output:** true
    **Explanation** : Both S and T become "".
    

**Example 3:**

    
    
    **Input:** S = "a##c", T = "#a#c"
    **Output:** true
    **Explanation** : Both S and T become "c".
    

**Example 4:**

    
    
    **Input:** S = "a#c", T = "b"
    **Output:** false
    **Explanation** : S becomes "c" while T becomes "b".
    

**Note** :

  1. `1 <= S.length <= 200`
  2. `1 <= T.length <= 200`
  3. `S` and `T` only contain lowercase letters and `'#'` characters.

 **Follow up:**

  * Can you solve it in `O(N)` time and `O(1)` space?

### Solution 1
 **Intuition** :  
The intuition and quick methode is to find the final text.  
You can just use a string if you don't care time complexity on string
modification.  
Or you can use a stack or string builder to do it in O(N).

 **2-line Python:**

    
    
         def backspaceCompare(self, S, T):
            back = lambda res, c: res[:-1] if c == '#' else res + c
            return reduce(back, S, "") == reduce(back, T, "")
    

Use stack to avoid string modification. O(N)

    
    
        def backspaceCompare(self, S, T):
            def back(res, c):
                if c != '#': res.append(c)
                elif res: res.pop()
                return res
            return reduce(back, S, []) == reduce(back, T, [])
    

**Follow up** :

Can you do it in O(N) time and O(1) space?  
I believe you have one difficulty here: When we meet a char, we are not sure
if it will be still there or be deleted.

However, we can do a back string compare (just like the title of problem).  
If we do it backward, we meet a char and we can be sure this char won't be
deleted.  
If we meet a '#', it tell us we need to skip next lowercase char.

**C++:**

    
    
        bool backspaceCompare(string S, string T) {
             for (int i = S.length() - 1, j = T.length() - 1;;i--, j--){
                for (int b = 0; i >= 0 && (b || S[i] == '#'); --i) b += S[i] == '#' ? 1 : -1;
                for (int b = 0; j >= 0 && (b || T[j] == '#'); --j) b += T[j] == '#' ? 1 : -1;
                if (i < 0 || j < 0 || S[i] != T[j]) return i == -1 && j == -1;
            }
        }
    

**Java:**

    
    
        public boolean backspaceCompare(String S, String T) {
             for (int i = S.length() - 1, j = T.length() - 1;; i--, j--) {
                for (int b = 0; i >= 0 && (b > 0 || S.charAt(i) == '#'); --i) b += S.charAt(i) == '#' ? 1 : -1;
                for (int b = 0; j >= 0 && (b > 0 || T.charAt(j) == '#'); --j) b += T.charAt(j) == '#' ? 1 : -1;
                if (i < 0 || j < 0 || S.charAt(i) != T.charAt(j)) return i == -1 && j == -1;
            }
        }
    

**Update 2018-08-30**

People complained readability.  
So I expand this solution..  
Hope it's better.

The idea is that, read next letter from end to start.  
If we meet `#`, we increase the number we need to step back, until `back = 0`

**C++:**

    
    
        bool backspaceCompare(string S, string T) {
            int  i = S.length() - 1, j = T.length() - 1;
            while (1) {
                for (int back = 0; i >= 0 && (back || S[i] == '#'); --i)
                    back += S[i] == '#' ? 1 : -1;
                for (int back = 0; j >= 0 && (back || T[j] == '#'); --j)
                    back += T[j] == '#' ? 1 : -1;
                if (i >= 0 && j >= 0 && S[i] == T[j])
                    i--, j--;
                else
                    return i == -1 && j == -1;
            }
        }
    

**Java:**

    
    
        public boolean backspaceCompare(String S, String T) {
            int  i = S.length() - 1, j = T.length() - 1;
            while (true) {
                for (int back = 0; i >= 0 && (back > 0 || S.charAt(i) == '#'); --i)
                    back += S.charAt(i) == '#' ? 1 : -1;
                for (int back = 0; j >= 0 && (back > 0 || T.charAt(j) == '#'); --j)
                    back += T.charAt(j) == '#' ? 1 : -1;
                if (i >= 0 && j >= 0 && S.charAt(i) == T.charAt(j)) {
                    i--; j--;
                } else
                    return i == -1 && j == -1;
            }
        }
    

**Python:**

    
    
        def backspaceCompare(self, S, T):
             i, j = len(S) - 1, len(T) - 1
            backS = backT = 0
            while True:
                while i >= 0 and (backS or S[i] == '#'):
                    backS += 1 if S[i] == '#' else -1
                    i -= 1
                while j >= 0 and (backT or T[j] == '#'):
                    backT += 1 if T[j] == '#' else -1
                    j -= 1
                if not (i >= 0 and j >= 0 and S[i] == T[j]):
                    return i == j == -1
                i, j = i - 1, j - 1
    


### Solution 2
Scan the String from right to left. Maintain the "#" count. Increment 'count'
variable if the character encountered is '#'. Otherwise decrement the count
and if the count is 0, append that character to the result.  
Perform above for both the strings, compare the results and return true/false
accordingly.

JAVA:

    
    
    class Solution {
        
        private String getString(String str) {
            int n=str.length(), count=0;
            String result="";
            for(int i=n-1; i>=0; i--) {
                char ch=str.charAt(i);
                if(ch=='#') 
                    count++;
                else {
                    if(count>0)
                        count--;
                    else {
                        result+=ch;
                    }                     
                }
            }
            return result;
        }
        
        public boolean backspaceCompare(String S, String T) {
            return getString(S).equals(getString(T));
        }
    }
    

C++

    
    
    class Solution {
    public:    
        string getString(string &str) {
            int n=str.length(), count=0;
            string result="";
            for(int i=n-1; i>=0; i--) {
                char ch=str[i];
                if(ch=='#') 
                    count++;
                else {
                    if(count>0)
                        count--;
                    else {
                        result+=ch;
                    }                     
                }
            }
            return result;        
        }
        
        bool backspaceCompare(string S, string T) {
            return getString(S)==getString(T);
        }
    };
    


### Solution 3
    
    
    /**
        1. Thoughts
            - from back to front
            - if count > 0 && != #, skip
            - if count == 0 && different, return false
            - if #, count++
            - if one ends, the other count == 0 && has any char return false
            - else return true
    */
    class Solution {
        public boolean backspaceCompare(String S, String T) {
            int i = S.length()-1;
            int j = T.length()-1;
            int countS = 0;
            int countT = 0;
            while (i >= 0 || j >= 0) {
                while (i >= 0 && (countS > 0 || S.charAt(i) == '#')) {
                    if (S.charAt(i) == '#') countS++;
                    else countS--;
                    i--;
                }
                char left = i < 0 ? '@' : S.charAt(i);
                while (j >= 0 && (countT > 0 || T.charAt(j) == '#')) {
                    if (T.charAt(j) == '#') countT++;
                    else countT--;
                    j--;
                }
                char right = j < 0 ? '@' : T.charAt(j);
                if (left != right) return false;
                i--;
                j--;
            }
            return true;
        }
    }
    



