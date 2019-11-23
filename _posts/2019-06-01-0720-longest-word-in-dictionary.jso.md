---
layout: post
title: 720. Longest Word in Dictionary
---
### Question
Given a list of strings `words` representing an English Dictionary, find the
longest word in `words` that can be built one character at a time by other
words in `words`. If there is more than one possible answer, return the
longest word with the smallest lexicographical order.

If there is no answer, return the empty string.

 **Example 1:**  

    
    
     **Input:** 
    words = ["w","wo","wor","worl", "world"]
    **Output:** "world"
    **Explanation:** 
    The word "world" can be built one character at a time by "w", "wo", "wor", and "worl".
    

**Example 2:**  

    
    
    **Input:** 
    words = ["a", "banana", "app", "appl", "ap", "apply", "apple"]
    **Output:** "apple"
    **Explanation:** 
    Both "apply" and "apple" can be built from other words in the dictionary. However, "apple" is lexicographically smaller than "apply".
    

**Note:**

* All the strings in the input will only contain lowercase letters.
* The length of `words` will be in the range `[1, 1000]`.
* The length of `words[i]` will be in the range `[1, 30]`.

### Solution 1
  1. Sort the words alphabetically, therefore shorter words always comes before longer words;
  2. Along the sorted list, populate the words that can be built;
  3. Any prefix of a word must comes before that word.

 **Java**

    
    
     class Solution {
        public String longestWord(String[] words) {
            Arrays.sort(words);
            Set<String> built = new HashSet<String>();
            String res = "";
            for (String w : words) {
                if (w.length() == 1 || built.contains(w.substring(0, w.length() - 1))) {
                    res = w.length() > res.length() ? w : res;
                    built.add(w);
                }
            }
            return res;
        }
    }
    

**C++**

    
    
     class Solution {
    public:
        string longestWord(vector<string>& words) {
            sort(words.begin(), words.end());
            unordered_set<string> built;
            string res;
            for (string w : words) {
                if (w.size() == 1 || built.count(w.substr(0, w.size() - 1))) {
                    res = w.size() > res.size() ? w : res;
                    built.insert(w);
                }
            }
            return res;
        }
    };
    


### Solution 2
Firstly sort the words to ensure that we iterate in a lexicographical fashion.
Also, maintain a set of seen words to check whether there is a "path" to the
current word we are iterating through. We can lengthen the path when we see
that there is a previous word that meets the criteria. Lastly, in every
iteration where there is a path lengthening, check to see if we can update the
`longest_word`.

 _By Yangshun_

    
    
    class Solution(object):
        def longestWord(self,  words):
            words.sort()
            words_set, longest_word = set(['']), ''
            for word in words:
                if word[:-1] in words_set:
                    words_set.add(word)
                    if len(word) > len(longest_word):
                        longest_word = word
            return longest_word
    


### Solution 3
Build a trie in the normal way, then do a dfs to find the longest
**continuous** downward path from the root. This is not a particularly hard
question in the context of trie, the point of this solution is to present a
generic way of trie building and inserting that can be easily adapted to
similar questions. Code:

    
    
     class Solution {
        public String longestWord(String[] words) {
            TrieNode root = new TrieNode ();
            root.word = "-";
            for (String word : words)
                root.insert (word);
            return dfs (root, "");
        }
    
        String dfs (TrieNode node, String accum) {
            if (node == null || node.word.length () == 0)
                return accum;
            String res = "";
            if (!node.word.equals ("-"))
                accum = node.word;
            for (TrieNode child : node.links) {
                String curRes = dfs (child, accum);
                if (curRes.length () > res.length () || (curRes.length () == res.length () && curRes.compareTo (res) < 0))
                    res = curRes;
            }
            return res;
        }
    
        /* Hand write this class every time you need to so you can remember well */
        static class TrieNode {
            String word = "";
            TrieNode[] links = new TrieNode[26];
    
            void insert (String s) {
                char[] chs = s.toCharArray ();
                TrieNode curNode = this;
                for (int i = 0; i < chs.length; i++) {
                    int index = chs[i] - 'a';
                    if (curNode.links[index] == null)
                        curNode.links[index] = new TrieNode ();
                    curNode = curNode.links[index];
                }
                curNode.word = s;
            }
        }
    }
    

A typical trie for the list of `"ab", "ac"`:  
![](http://i63.tinypic.com/2jbtwjl.png)



