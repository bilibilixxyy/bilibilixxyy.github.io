---
layout: post
title: 916. Decoded String at Index
---
### Question
An encoded string `S` is given.  To find and write the _decoded_ string to a
tape, the encoded string is read **one character at a time**  and the
following steps are taken:

  * If the character read is a letter, that letter is written onto the tape.
  * If the character read is a digit (say `d`), the entire current tape is repeatedly written `d-1` more times in total.

Now for some encoded string `S`, and an index `K`, find and return the `K`-th
letter (1 indexed) in the decoded string.



 **Example 1:**

    
    
     **Input:** S = "leet2code3", K = 10
    **Output:** "o"
    **Explanation:**
    The decoded string is "leetleetcodeleetleetcodeleetleetcode".
    The 10th letter in the string is "o".
    

**Example 2:**

    
    
    **Input:** S = "ha22", K = 5
    **Output:** "h"
    **Explanation:**
    The decoded string is "hahahaha".  The 5th letter is "h".
    

**Example 3:**

    
    
    **Input:** S = "a2345678999999999999999", K = 1
    **Output:** "a"
    **Explanation:**
    The decoded string is "a" repeated 8301530446056247680 times.  The 1st letter is "a".
    



 **Note:**

  1. `2 <= S.length <= 100`
  2. `S` will only contain lowercase letters and digits `2` through `9`.
  3. `S` starts with a letter.
  4. `1 <= K <= 10^9`
  5. The decoded string is guaranteed to have less than `2^63` letters.

### Solution 1
We decode the string and `N` keeps the length of decoded string, until `N >=
K`.  
Then we go back from the decoding position.  
If it's `S[i] = d` is a digit, then `N = N / d` before repeat and `K = K % N`
is what we want.  
If it's `S[i] = c` is a character, we return `c` if `K == 0` or `K == N`

 **C++:**

    
    
        string decodeAtIndex(string S, int K) {
            long N =  0, i;
            for (i = 0; N < K; ++i)
                N = isdigit(S[i]) ? N * (S[i] - '0') : N + 1;
            while (i--)
                if (isdigit(S[i]))
                    N /= S[i] - '0', K %= N;
                else if (K % N-- == 0)
                    return string(1, S[i]);
            return "lee215";
        }
    

**Python:**

    
    
        def decodeAtIndex(self, S, K):
            N =  0
            for i, c in enumerate(S):
                N = N * int(c) if c.isdigit() else N + 1
                if K <= N: break
            for j in range(i, -1, -1):
                c = S[j]
                if c.isdigit():
                    N /= int(c)
                    K %= N
                else:
                    if K == N or K == 0: return c
                    N -= 1
    


### Solution 2
    
    
    class Solution {
        public String decodeAtIndex(String S, int K) {
            for (int len = 0, i = 0; i < S.length(); i++) {
                if (Character.isDigit(S.charAt(i))) {
                    int num = S.charAt(i) - '0', count = 0;
                    for (; count < num - 1 && K > len; count++) {
                        K -= len;
                    }
                    if (count != num - 1) {
                        return decodeAtIndex(S, K);
                    } else {
                        len *= num;
                    }
                } else {
                    len++;
                    K--;
                    if (K == 0) {
                        return String.valueOf(S.charAt(i));
                    }
                }
            }
            return "";
        }
    }
    


### Solution 3
As we go through the string, we increment `len` as we encounter a letter, and
multiply it if we see a number.

  * If, after the increment, `len` is equal `K`, we return the current letter.
  * If `len` after the multiply is equal or greater than `K`, we call our function recursively for `K % len`.

    
    
    string decodeAtIndex(string &S, int K, long long len = 0) {
      for (auto i = 0; i < S.size(); ++i) {
        if (isalpha(S[i])) {
          if (++len == K) return string(1, S[i]);
        }
        else {
          if (len * (S[i] - '0') >= K) return decodeAtIndex(S, K % len == 0 ? len : K % len);
          len *= S[i] - '0';
        }
      }
    }
    



