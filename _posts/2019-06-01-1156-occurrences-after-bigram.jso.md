---
layout: post
title: 1156. Occurrences After Bigram
---
### Question
Given words `first` and `second`, consider occurrences in some `text` of the
form "`first second third`", where `second` comes immediately after `first`,
and `third` comes immediately after `second`.

For each such occurrence, add "`third`" to the answer, and return the answer.



 **Example 1:**

    
    
     **Input:** text = "alice is a good girl she is a good student", first = "a", second = "good"
    **Output:** ["girl","student"]
    

**Example 2:**

    
    
    **Input:** text = "we will we will rock you", first = "we", second = "will"
    **Output:** ["we","rock"]
    



 **Note:**

  1. `1 <= text.length <= 1000`
  2. `text` consists of space separated words, where each word consists of lowercase English letters.
  3. `1 <= first.length, second.length <= 10`
  4. `first` and `second` consist of lowercase English letters.

### Solution 1
First, compose our bigram as `first + " " + second + " "`. Thanks
[alreadydone](https://leetcode.com/alreadydone/) for the advice to add an
extra space in the end.

Then search for all bigram occurrences, extracting the word that follows.

    
    
    vector<string> findOcurrences(string text, string first, string second) {
      vector<string> res;
      auto bigram = first + " " + second + " ";
      auto p = text.find(bigram);
      while (p != string::npos) {
        auto p1 = p + bigram.size(), p2 = p1;
        while (p2 < text.size() && text[p2] != ' ') ++p2;
        res.push_back(text.substr(p1, p2 - p1));
        p = text.find(bigram, p + 1);
      }
      return res;
    }
    

# Complexity Analysis

Runtime: _O(n + m)_ , where _n_ is the size of the string, and _m_ \- size of
the bigram.

> A single find operation is _O(n + m)_ ; we can search several times but we
move forward and don't consider the part we searched already.

>

> Even if our string is `"a a a a ..."`, and bigram - `"a a "`, the total
number of operation will not exceed _2n_.

Memory: _O(m)_ to store the bigram, or _O(n)_ if we consider the memory for
the result.


### Solution 2
 **Method 1:**

Split the `text` into `words` array, then loop through it to check if previous
two words are `first` and `second`; If yes, add current word into list.

    
    
        public String[] findOcurrences(String text, String first, String second) {
            String[] words = text.split(" ");
            List<String> ans = new ArrayList<>();
            for (int i = 2; i < words.length; ++i) {
                if (first.equals(words[i - 2]) && second.equals(words[i - 1]))
                    ans.add(words[i]);
            }
            return ans.toArray(new String[0]);
        }
    

* * *

**Method 2:** bruteforce.

  1. Locate 1st and 2nd words, then look for the 3rd;
  2. start from the 2nd word, repeat the above 1. till no result exists.

    
    
        public String[] findOcurrences(String text, String first, String second) {
            List<String> ans = new ArrayList<>();
            int i = 0;
            while(i >= 0) {
                String two = first + ' ' + second + ' ';
                int idx = text.indexOf(two, i);
                int end = idx + two.length(), start = end;
                while (end < text.length() && text.charAt(end) != ' ') 
                    ++end;
                if (idx >= 0 && start < text.length()) 
                    ans.add(text.substring(start, end));
                else 
                    break;
                i = idx + first.length() + 1;
            }
            return ans.toArray(new String[0]);
        }
    


### Solution 3
See more code in my repo [LeetCode](https://github.com/lzl124631x/LeetCode)

    
    
    // OJ: https://leetcode.com/problems/occurrences-after-bigram/
    // Author: github.com/lzl124631x
    // Time: O(N)
    // Space: O(N)
    class Solution {
    public:
        vector<string> findOcurrences(string text, string first, string second) {
            vector<string> ans;
            istringstream ss(text);
            string prev2, prev, word;
            while (ss >> word) {
                if (prev2 == first && prev == second) ans.push_back(word);
                prev2 = prev;
                prev = word;
            }
            return ans;
        }
    };
    



