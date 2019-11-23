---
layout: post
title: 878. Shifting Letters
---
### Question
We have a string `S` of lowercase letters, and an integer array `shifts`.

Call the _shift_ of a letter, the next letter in the alphabet, (wrapping
around so that `'z'` becomes `'a'`).

For example, `shift('a') = 'b'`, `shift('t') = 'u'`, and `shift('z') = 'a'`.

Now for each `shifts[i] = x`, we want to shift the first `i+1` letters of `S`,
`x` times.

Return the final string after all such shifts to `S` are applied.

 **Example 1:**

    
    
     **Input:** S = "abc", shifts = [3,5,9]
    **Output:** "rpl"
    **Explanation:**
    We start with "abc".
    After shifting the first 1 letters of S by 3, we have "dbc".
    After shifting the first 2 letters of S by 5, we have "igc".
    After shifting the first 3 letters of S by 9, we have "rpl", the answer.
    

**Note:**

  1. `1 <= S.length = shifts.length <= 20000`
  2. `0 <= shifts[i] <= 10 ^ 9`

### Solution 1
 **Explanation** :  
One pass to count suffix sum of `shifts`.  
One pass to shift letters in string `S`  
You can combine 2 passes as I did in 2-lines C++ version.

 **Time Complexity** :  
O(N)

 **C++:**

    
    
        string shiftingLetters2(string S, vector<int> shifts) {
             for (int i = shifts.size() - 2; i >= 0; i--)
                shifts[i] = (shifts[i] + shifts[i + 1]) % 26;
            for (int i = 0; i < shifts.size(); i++)
                S[i] = (S[i] - 'a' + shifts[i]) % 26 + 'a';
            return S;
        }
    

**2-lines C++:**

    
    
        string shiftingLetters(string S, vector<int> sh) {
             for (int i = sh.size() - 1, m = 0; i >= 0; --i, m %= 26)
                S[i] = ((S[i] - 'a') + (m += sh[i])) % 26 + 'a';
            return S;
        }
    

**Java:**

    
    
        public String shiftingLetters(String S, int[] shifts) {
            StringBuilder res = new StringBuilder(S);
             for (int i = shifts.length - 2; i >= 0; i--)
                shifts[i] = (shifts[i] + shifts[i + 1]) % 26;
            for (int i = 0; i < S.length(); i++)
                res.setCharAt(i, (char)((S.charAt(i) - 'a' + shifts[i]) % 26 + 'a'));
            return res.toString();
        }
    

**2-lines Python:**

    
    
        def shiftingLetters(self, S, shifts):
             for i in range(len(shifts) - 1)[::-1]: shifts[i] += shifts[i + 1]
            return "".join(chr((ord(c) - 97 + s) % 26 + 97) for c, s in zip(S, shifts))
    


### Solution 2
    
    
    class Solution {
        public String shiftingLetters(String S, int[] shifts) {
            char[] arr = S.toCharArray();
            int shift = 0;
            for (int i = arr.length - 1; i >= 0; i--) {
                shift = (shift + shifts[i]) % 26;
                arr[i] = (char)((arr[i] - 'a' + shift) % 26 + 'a');
            }
            return new String(arr);
        }
    }
    


### Solution 3
    
    
    string shiftingLetters(string S, vector<int>& shifts) {
        for (int i = S.size() - 1, p = 0; i >= 0; i--)
            S[i] = (S[i] - 'a' + (p += shifts[i] % 26)) % 26 + 'a';
        return S;
    }
    



