---
layout: post
title: 827. Expressive Words
---
### Question
Sometimes people repeat letters to represent extra feeling, such as "hello" ->
"heeellooo", "hi" -> "hiiii".  In these strings like "heeellooo", we have
_groups_ of adjacent letters that are all the same:   "h", "eee", "ll", "ooo".

For some given string `S`, a query word is _stretchy_ if it can be made to be
equal to `S` by any number of applications of the following _extension_
operation: choose a group consisting of  characters `c`, and add some number
of characters `c` to the group so that the size of the group is 3 or more.

For example, starting with "hello", we could do an extension on the group "o"
to get "hellooo", but we cannot get "helloo" since the group "oo" has size
less than 3.  Also, we could do another extension like "ll" -> "lllll" to get
"helllllooo".  If `S = "helllllooo"`, then the query word "hello" would be
stretchy because of these two extension operations: `query = "hello" ->
"hellooo" -> "helllllooo" = S`.

Given a list of query words, return the number of words that are stretchy.



    
    
     **Example:**
    **Input:** 
    S = "heeellooo"
    words = ["hello", "hi", "helo"]
    **Output:** 1
    **Explanation:** 
    We can extend "e" and "o" in the word "hello" to get "heeellooo".
    We can't extend "helo" to get "heeellooo" because the group "ll" is not size 3 or more.
    



 **Notes:**

  * `0 <= len(S) <= 100`.
  * `0 <= len(words) <= 100`.
  * `0 <= len(words[i]) <= 100`.
  * `S` and all words in `words` consist only of lowercase letters

### Solution 1
Loop through all words. `check(string S, string W)` checks if `W` is stretchy
to `S`.

In `check` function, use two pointer:

  1. If `S[i] == W[j`, `i++, j++`
  2. If `S[i - 2] == S[i - 1] == S[i]` or `S[i - 1] == S[i] == S[i + 1]`, `i++`
  3. return false

 **C++** :

    
    
        int expressiveWords(string S, vector<string>& words) {
            int res = 0;
            for (auto &W : words) if (check(S, W)) res++;
            return res;
        }
    
        bool check(string S, string W) {
            int n = S.size(), m = W.size(), j = 0;
            for (int i = 0; i < n; i++)
                if (j < m && S[i] == W[j]) j++;
                else if (i > 1 && S[i - 2] == S[i - 1] && S[i - 1] == S[i]);
                else if (0 < i && i < n - 1 && S[i - 1] == S[i] && S[i] == S[i + 1]);
                else return false;
            return j == m;
        }
    

**Java** :

    
    
        public int expressiveWords(String S, String[] words) {
            int res = 0;
            for (String W : words) if (check(S, W)) res++;
            return res;
        }
        public boolean check(String S, String W) {
            int n = S.length(), m = W.length(), j = 0;
            for (int i = 0; i < n; i++)
                if (j < m && S.charAt(i) == W.charAt(j)) j++;
                else if (i > 1 && S.charAt(i) == S.charAt(i - 1) && S.charAt(i - 1) == S.charAt(i - 2));
                else if (0 < i && i < n - 1 && S.charAt(i - 1) == S.charAt(i) && S.charAt(i) == S.charAt(i + 1));
                else return false;
            return j == m;
        }
    

**Python** :

    
    
        def expressiveWords(self, S, words):
            return sum(self.check(S, W) for W in words)
    
        def check(self, S, W):
            i, j, n, m = 0, 0, len(S), len(W)
            for i in range(n):
                if j < m and S[i] == W[j]: j += 1
                elif S[i - 1:i + 2] != S[i] * 3 != S[i - 2:i + 1]: return False
            return j == m
    

Another approach use 4 pointers, but will be much easier to undersand and
debug.

**C++:**

    
    
        bool check(string S, string W) {
            int n = S. size(), m = W.size(), i = 0, j = 0;
            for (int i2 = 0, j2 = 0; i < n && j < m; i = i2, j = j2) {
                if (S[i] != W[j]) return false;
                while (i2 < n && S[i2] == S[i]) i2++;
                while (j2 < m && W[j2] == W[j]) j2++;
                if (i2 - i != j2 - j && i2 - i < max(3, j2 - j)) return false;
            }
            return i == n && j == m;
        }
    

**Java:**

    
    
        public boolean check(String S, String W) {
             int n = S.length(), m = W.length(), i = 0, j = 0;
            for (int i2 = 0, j2 = 0; i < n && j < m; i = i2, j = j2) {
                if (S.charAt(i) != W.charAt(j)) return false;
                while (i2 < n && S.charAt(i2) == S.charAt(i)) i2++;
                while (j2 < m && W.charAt(j2) == W.charAt(j)) j2++;
                if (i2 - i != j2 - j && i2 - i < Math.max(3, j2 - j)) return false;
            }
            return i == n && j == m;
        }
    

**Python:**

    
    
        def check(self, S, W):
             i, j, i2, j2, n, m = 0, 0, 0, 0, len(S), len(W)
            while i < n and j < m:
                if S[i] != W[j]: return False
                while i2 < n and S[i2] == S[i]: i2 += 1
                while j2 < m and W[j2] == W[j]: j2 += 1
                if i2 - i != j2 - j and i2 - i < max(3, j2 - j): return False
                i, j = i2, j2
            return i == n and j == m
    

**Update 2018-06-05** :  
I updated all solution and add another approach, hope it helps.  
The first one is similar to @mzchen's solution.  
The second one will be more straight forward.


### Solution 2
I read it for about 10 times, try my best, but fail to understand the real
meaning of the problem.

We are working on algorithm problems, not the puzzles.

Thanks a lot.


### Solution 3
We have two pointers, use `i` to scan `S`, and use `j` to scan each `word` in
`words`.  
Firstly, for `S` and `word`, we can calculate the length of the susbtrings
which contains the repeated letters of the letter currently pointed by the two
pointers, and get `len1` and `len2`.

The two letters currently pointed by the two pointers must be equal, otherwise
the `word` is not stretchy, we return false. Then, if we find that `len1` is
smaller than 3, it means the letter cannot be extended, so `len1` must equals
to `len2`, otherwise this `word` is not stretchy. In the other case, if `len1`
equals to or larger than 3, we must have `len1` smaller than `len2`, otherwise
there are not enough letters in `S` to match the letters in `word`.

Finally, if the `word` is stretchy, we need to guarantee that both of the two
pointers has scanned the whole string.

    
    
    class Solution {
        public int expressiveWords(String S, String[] words) {
            if (S == null || words == null) {
                return 0;
            }
            int count = 0;
            for (String word : words) {
                if (stretchy(S, word)) {
                    count++;
                }
            }
            return count;
        }
        
        public boolean stretchy(String S, String word) {
            if (word == null) {
                return false;
            }
            int i = 0;
            int j = 0;
            while (i < S.length() && j < word.length()) {
                if (S.charAt(i) == word.charAt(j)) {
                    int len1 = getRepeatedLength(S, i);
                    int len2 = getRepeatedLength(word, j);
                    if (len1 < 3 && len1 != len2 || len1 >= 3 && len1 < len2) {
                        return false;
                    }
                    i += len1;
                    j += len2;
                } else {
                    return false;
                }
            }
            return i == S.length() && j == word.length();
        }
        
        public int getRepeatedLength(String str, int i) {
            int j = i;
            while (j < str.length() && str.charAt(j) == str.charAt(i)) {
                j++;
            }
            return j - i;
        }
    }
    



