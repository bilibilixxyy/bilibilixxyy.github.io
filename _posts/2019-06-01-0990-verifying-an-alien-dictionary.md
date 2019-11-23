---
layout: post
title: 990. Verifying an Alien Dictionary
---
### Question
In an alien language, surprisingly they also use english lowercase letters,
but possibly in a different `order`. The `order` of the alphabet is some
permutation of lowercase letters.

Given a sequence of `words` written in the alien language, and the `order` of
the alphabet, return `true` if and only if the given `words` are sorted
lexicographicaly in this alien language.



 **Example 1:**

    
    
     **Input:** words = ["hello","leetcode"], order = "hlabcdefgijkmnopqrstuvwxyz"
    **Output:** true
    **Explanation:** As 'h' comes before 'l' in this language, then the sequence is sorted.
    

**Example 2:**

    
    
    **Input:** words = ["word","world","row"], order = "worldabcefghijkmnpqstuvxyz"
    **Output:** false
    **Explanation:** As 'd' comes after 'l' in this language, then words[0] > words[1], hence the sequence is unsorted.
    

**Example 3:**

    
    
    **Input:** words = ["apple","app"], order = "abcdefghijklmnopqrstuvwxyz"
    **Output:** false
    **Explanation:** The first three characters "app" match, and the second string is shorter (in size.) According to lexicographical rules "apple" > "app", because 'l' > '∅', where '∅' is defined as the blank character which is less than any other character ([More info](https://en.wikipedia.org/wiki/Lexicographical_order)).
    



 **Note:**

  1. `1 <= words.length <= 100`
  2. `1 <= words[i].length <= 20`
  3. `order.length == 26`
  4. All characters in `words[i]` and `order` are english lowercase letters.

### Solution 1
 **Explanation**  
Build a transform mapping from `order`,  
Find all alien words with letters in normal order.

For example, if we have `order = "xyz..."`  
We can map the word `"xyz"` to `"abc"` or `"123"`

Then we check if all words are in sorted order.

 **Time complexity**  
`O(NS)`

 **Java**

    
    
        int[] mapping = new int[ 26];
        public boolean isAlienSorted(String[] words, String order) {
            for (int i = 0; i < order.length(); i++)
                mapping[order.charAt(i) - 'a'] = i;
            for (int i = 1; i < words.length; i++)
                if (compare(words[i - 1], words[i]) > 0)
                    return false;
            return true;
        }
    
        int compare(String s1, String s2) {
            int n = s1.length(), m = s2.length(), cmp = 0;
            for (int i = 0, j = 0; i < n && j < m && cmp == 0; i++, j++) {
                cmp = mapping[s1.charAt(i) - 'a'] - mapping[s2.charAt(j) - 'a'];
            }
            return cmp == 0 ? n - m : cmp;
        }
    

**C++** :

    
    
        bool isAlienSorted(vector<string> words, string order) {
            int mapping[26];
            for (int i = 0; i < 26; i++)
                mapping[order[i] - 'a'] = i;
            for (string &w : words)
                for (char &c : w)
                    c = mapping[c - 'a'];
            return is_sorted(words.begin(), words.end());
        }
    

**Python**

    
    
        def isAlienSorted( self, words, order):
            m = {c: i for i, c in enumerate(order)}
            words = [[m[c] for c in w] for w in words]
            return all(w1 <= w2 for w1, w2 in zip(words, words[1:]))
    

**Python 1-line**  
Slow, Just for fun

    
    
         def isAlienSorted(self, words, order):
            return words == sorted(words, key=lambda w: map(order.index, w))
    


### Solution 2
Compare adjacent words char by char according to the given order.  
Loop through the `words`, if the char of previous word `.vs.` that of current
word:

  1. less than;  
no need to compare the following chars, continue to check next two adjacet
words;

  2. greater than;  
return `false`

  3. if current word is the prefix of and shorter than previous word;  
return `false`

  4. if never encounter `false` during loop, return `true`.

 **Analysis:**  
Outer loop iterates `m (= words.length)` times, and inner loop runs `n (=
A[0].length())` times;  
`map` cost O(1) space.

Therefore, **Time: O(`mn`), space: O(`1`).**

    
    
         public boolean isAlienSorted(String[] words, String order) {
            int[] map = new int[26];
            for (int i = 0; i < 26; ++i) { map[order.charAt(i) - 'a'] = i; } // construct mapping to the given order.
            outer:
            for (int i = 1; i < words.length; ++i) { // Verify the adjacent words order.
                for (int j = 0; j < words[i - 1].length() && j < words[i].length(); ++j) { // compare the 2 words char by char.
                    int pre = words[i - 1].charAt(j) - 'a', cur = words[i].charAt(j) - 'a'; // corresponding indexes in mapping array `map`.
                    if (map[pre] > map[cur]) { return false; } // if their relation is NOT compliant with `order`, false.
                    else if (map[pre] < map[cur]) { continue outer; } // if current char > previous char, no need to check following chars.
                } // NOTE: only when 1 word is the prefix of the other, can the code excute the following if clause.
                if (words[i - 1].length() > words[i].length()) { return false; } //  current word must NOT be the prefix of and shorter than previous word.
            }
            return true;
        }
    

The above code can be converted to the following more readable form:
decouplling the `compare` logic from the original method. Credit to
@tpalmer1243.

    
    
        public boolean isAlienSorted(String[] words, String order) {
            int[] map = new int[26];
            for (int i = 0; i < 26; ++i) { map[order.charAt(i) - 'a'] = i; } // construct mapping to the give order.
            for (int i = 1; i < words.length; ++i) { // Verify the ajacent words order. 
                if (compare(words[i - 1], words[i], map) > 0) { return false; } 
            }
            return true;
        }
        private int compare(String s, String t, int[] map) { // compare the 2 words char by char.
            int m = s.length(), n = t.length();
            for (int i = 0; i < m && i < n; ++i) {
                int cmp = map[s.charAt(i) - 'a'] - map[t.charAt(i) - 'a']; // compare according to the given order.
                if (cmp != 0) { return cmp; } // if not equal, return the comparison result.
            }
            return m - n; // when one word is the prefix of the other, compare their sizes.
        }
    


### Solution 3
    
    
    class Solution {
                
        public boolean isAlienSorted(String[] words, String order) {
            int[] map = new int[26];
            for (int i = 0; i < order.length(); i++) {
                map[order.charAt(i) - 'a'] = i;
            }
            for (int i = 1; i < words.length; i++) {
                String prev = words[i - 1];
                String current = words[i];
                if (!small(prev, current, map)) return false;
            }
            return true;
        }
        
        private boolean small(String s1, String s2, int[] map) {
            int len = Math.min(s1.length(), s2.length());
            for (int i = 0; i < len; i++) {
                if (map[s1.charAt(i) - 'a'] != map[s2.charAt(i) - 'a']) {
                    return map[s1.charAt(i) - 'a'] < map[s2.charAt(i) - 'a'];
                }
            }
            return s1.length() <= s2.length();
        }
        
    }
    



