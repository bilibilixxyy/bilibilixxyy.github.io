---
layout: post
title: 1129. Longest String Chain
---
### Question
Given a list of words, each word consists of English lowercase letters.

Let's say `word1` is a predecessor of `word2` if and only if we can add
exactly one letter anywhere in `word1` to make it equal to `word2`.  For
example, `"abc"` is a predecessor of `"abac"`.

A _word chain  _is a sequence of words `[word_1, word_2, ..., word_k]` with `k
>= 1`, where `word_1` is a predecessor of `word_2`, `word_2` is a predecessor
of `word_3`, and so on.

Return the longest possible length of a word chain with words chosen from the
given list of `words`.



 **Example 1:**

    
    
     **Input:** ["a","b","ba","bca","bda","bdca"]
    **Output:** 4
    **Explanation** : one of the longest word chain is "a","ba","bda","bdca".
    



 **Note:**

  1. `1 <= words.length <= 1000`
  2. `1 <= words[i].length <= 16`
  3. `words[i]` only consists of English lowercase letters.

### Solution 1
Sort the `words` by word's length. (also can appply bucket sort)  
For each word, loop on all possible previous word with 1 letter missing.  
If we have seen this previous word, update the longest chain for the current
word.  
Finally return the longest word chain.  
  

 **Python:**

    
    
        def longestStrChain(self, words):
             dp = {}
            for w in sorted(words, key=len):
                dp[w] = max(dp.get(w[:i] + w[i + 1:], 0) + 1 for i in xrange(len(w)))
            return max(dp.values())
    

  

**Java**  
Equivalent in Java suggested by @noname_minion

    
    
         public int longestStrChain(String[] words) {
            Map<String, Integer> dp = new HashMap<>();
            Arrays.sort(words, (a, b)->a.length() - b.length());
            for (String word : words) {
                int max = 0;
                for (int i = 0; i < word.length(); ++i) {
                    String prev = word.substring(0, i) + word.substring(i + 1);
                    max = Math.max(max, dp.getOrDefault(prev, 0) + 1);
                }
                dp.put(word, max);
            }
            int res = 0;
            for (int v : dp.values()) {
                res = Math.max(res, v);
            }
            return res;
        }
    

**C++**  
Equivalent in C++ suggested by @thebotman

    
    
     class Solution {
        static bool compare(const string &s1, const string &s2) {
            return s1.length() < s2.length();
        }
    
    public:
        int longestStrChain(vector<string>& words) {
            sort(words.begin(), words.end(), compare);
            unordered_map<string, int> dp;
            for (string w : words) {
                int maxi = 0;
                for (int i = 0; i < w.length(); i++) {
                    string word = w.substr(0, i) + w.substr(i + 1);
                    maxi = max(maxi, dp[word] + 1);
                }
                dp[w] = maxi;
            }
            int result = 0;
            for (auto m : dp) {
                result = max(result, m.second);
            }
            return result;
        }
    };
    


### Solution 2
  1. Sort the strings by their lengths
  2. Start from shorter words (bottom up DP)
  3. During the loop: for each word, iterate through its length and each time delete 1 char. Update the longest string chain length in the hashmap for the current word.

    
    
    class Solution {
        public int longestStrChain(String[] words) {
            if (words == null || words.length == 0) return 0;
            int res = 0;
            Arrays.sort(words, new Comparator<String>() {
                public int compare(String str1, String str2) {
                    return str1.length() - str2.length();
                }
            });
            HashMap<String, Integer> map = new HashMap();
            for (String word : words) {
                if (map.containsKey(word)) continue;
                map.put(word, 1);
                for (int i = 0; i < word.length(); i++) {
                    StringBuilder sb = new StringBuilder(word);
                    sb.deleteCharAt(i);
                    String next = sb.toString();
                    if (map.containsKey(next) && map.get(next) + 1 > map.get(word)) {
                        map.put(word, map.get(next) + 1);
                    }
                }
                if (map.get(word) > res) res = map.get(word);
            }
            return res;
        }   
    }
    


### Solution 3
    
    
    class Solution {
        public int longestStrChain(String[] words) {
            Arrays.sort(words, (a, b) -> a.length() - b.length());
            
            Map<String, Integer> dp = new HashMap();
            for (String word : words) {
                dp.put(word, 1);
            }
        
            int max = 0;
            for (String word : words) {
                for (int i = 0; i < word.length(); ++i) {   // delete one char from current word to find the previous string
                    String prev = word.substring(0, i) + word.substring(i+1);
                    if (dp.containsKey(prev)) {
                        dp.put(word, Math.max(dp.get(word), dp.get(prev) + 1));
                    }
                }
                max = Math.max(max, dp.get(word));
            }
            return max;
        }
    }
    



