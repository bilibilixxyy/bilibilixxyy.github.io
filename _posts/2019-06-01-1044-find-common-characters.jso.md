---
layout: post
title: 1044. Find Common Characters
---
### Question
Given an array `A` of strings made only from lowercase letters, return a list
of all characters that show up in all strings within the list **(including
duplicates)**.   For example, if a character occurs 3 times in all strings but
not 4 times, you need to include that character three times in the final
answer.

You may return the answer in any order.



 **Example 1:**

    
    
     **Input:** ["bella","label","roller"]
    **Output:** ["e","l","l"]
    

**Example 2:**

    
    
    **Input:** ["cool","lock","cook"]
    **Output:** ["c","o"]
    



 **Note:**

  1. `1 <= A.length <= 100`
  2. `1 <= A[i].length <= 100`
  3. `A[i][j]` is a lowercase letter

### Solution 1
    
    
        def commonChars(self, A):
            res = collections.Counter(A[0])
            for a in A:
                res &= collections.Counter(a)
            return list(res.elements())
    

1-line version

    
    
        def commonChars(self, A):
            return list(reduce(collections.Counter.__and__, map(collections.Counter, A)).elements())
    


### Solution 2
For each string, we count characters in `cnt1`. Then, we track the minimum
count for each character in `cnt`.

    
    
    vector<string> commonChars(vector<string>& A) {
      vector<int> cnt(26, INT_MAX);
      vector<string> res;
      for (auto s : A) {
        vector<int> cnt1(26, 0);
        for (auto c : s) ++cnt1[c - 'a'];
        for (auto i = 0; i < 26; ++i) cnt[i] = min(cnt[i], cnt1[i]);
      }
      for (auto i = 0; i < 26; ++i)
        for (auto j = 0; j < cnt[i]; ++j) res.push_back(string(1, i + 'a'));
      return res;
    }
    

## Complexity Analysis

Runtime: _O(n)_ , where _n_ is the total number of characters.  
Memory: _O(1)_ (we use two fixed-size vectors).


### Solution 3
Initialize `count` array with `Integer.MAX_VALUE`, loop through the input to
count the chars in each string; then find out the minimum for each char.

    
    
        public List<String> commonChars(String[] A) {
            List<String> ans = new ArrayList<>();
            int[] count = new int[26]; 
            Arrays.fill(count, Integer.MAX_VALUE);
            for (String str : A) {
                int[] cnt = new int[26];
                for (int i = 0; i < str.length(); ++i) { ++cnt[str.charAt(i) - 'a']; } // count each char's frequency in string str.
                for (int i = 0; i < 26; ++i) { count[i] = Math.min(cnt[i], count[i]); } // update minimum frequency.
            }
            for (char c = 'a'; c <= 'z'; ++c) {
                while (count[c - 'a']-- > 0) { ans.add("" + c); }
            }
            return ans;
        }
    

**Analysis:**  
 **Time: O(n)** , where `n` is the total number of characters in A;  
 **extra space: O(1)** , excludes output/return space.

 **Q & A**:

Q: What does `++cnt[c - 'a'];` mean?

A:  
count the frequency of chars in String str.

## c : cnt[c - 'a']

## 'a': cnt['a' - 'a'] = cnt[0]  
'b': cnt['b' - 'a'] = cnt[1]  
'c': cnt['c' - 'a'] = cnt[2]  
...  
'z': cnt['z' - 'a'] = cnt[25]

if char `c` represents `'x'`, then `cnt[c - 'a'] = cnt[23]`. That is, when
encountering char 'x', we increase cnt[23] by 1.

Therefore, after traversal of all chars in String `str`, we have the frequency
(number of occurrence) of each char in `cnt`.



