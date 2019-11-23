---
layout: post
title: 1080. Camelcase Matching
---
### Question
A query word matches a given `pattern` if we can insert **lowercase** letters
to the pattern word so that it equals the `query`. (We may insert each
character at any position, and may insert 0 characters.)

Given a list of `queries`, and a `pattern`, return an `answer` list of
booleans, where `answer[i]` is true if and only if `queries[i]` matches the
`pattern`.



 **Example 1:**

    
    
     **Input:** queries = ["FooBar","FooBarTest","FootBall","FrameBuffer","ForceFeedBack"], pattern = "FB"
    **Output:** [true,false,true,true,false]
    **Explanation:**
    "FooBar" can be generated like this "F" + "oo" + "B" + "ar".
    "FootBall" can be generated like this "F" + "oot" + "B" + "all".
    "FrameBuffer" can be generated like this "F" + "rame" + "B" + "uffer".

**Example 2:**

    
    
    **Input:** queries = ["FooBar","FooBarTest","FootBall","FrameBuffer","ForceFeedBack"], pattern = "FoBa"
    **Output:** [true,false,true,false,false]
    **Explanation:**
    "FooBar" can be generated like this "Fo" + "o" + "Ba" + "r".
    "FootBall" can be generated like this "Fo" + "ot" + "Ba" + "ll".
    

**Example 3:**

    
    
    **Input:** queries = ["FooBar","FooBarTest","FootBall","FrameBuffer","ForceFeedBack"], pattern = "FoBaT"
    **Output:** [false,true,false,false,false]
    **Explanation:**
    "FooBarTest" can be generated like this "Fo" + "o" + "Ba" + "r" + "T" + "est".
    



 **Note:**

  1. `1 <= queries.length <= 100`
  2. `1 <= queries[i].length <= 100`
  3. `1 <= pattern.length <= 100`
  4. All strings consists only of lower and upper case English letters.

### Solution 1
For each string, macth it with the pattern and pass the result.

The match process uses i for query pointer and j for pattern pointer, each
iteration;

  1. If current char query[i] matches pattern[j], increase pattern pointer;
  2. if does not match and query[i] is lowercase, keep going;
  3. if does not match and query[i] is captalized, we should return false.

If this pattern matches, j should equal length of pattern at the end.

Hope this helps.

    
    
    class Solution {
        public List<Boolean> camelMatch(String[] queries, String pattern) {
            List<Boolean> res = new ArrayList<>();
            
            char[] patternArr = pattern.toCharArray();
            for (String query : queries) {
                boolean isMatch = match(query.toCharArray(), patternArr);
                res.add(isMatch);
            }
            
            return res;
        }
        
        private boolean match(char[] queryArr, char[] patternArr) {
            int j = 0;
            for (int i = 0; i < queryArr.length; i++) {
                if (j < patternArr.length && queryArr[i] == patternArr[j]) {
                    j++;
                } else if (queryArr[i] >= 'A' && queryArr[i] <= 'Z') {
                    return false;
                }
            }
            
            return j == patternArr.length;
        }
    }
    

Updated naming convention and coding style per comments by destiny130@ .
Thanks for your advice.

Possible improvement, special thanks to @Sithis:

  1. Use `new ArrayList<>(queries.length)` to allocate capacity up-front. This can avoid resizing and copying as the size of the array grows.

  2. `queryArr[i] >= 'A' && queryArr[i] <= 'Z'` can be replaced by built-in static method `Character.isUpperCase()`.


### Solution 2
##  **Solution 1: Check Subsequence**

 **Java**

    
    
         public List<Boolean> camelMatch(String[] queries, String pattern) {
            List<Boolean> res = new ArrayList<>();
            for (String query : queries)
                res.add(isMatch(query, pattern));
            return res;
        }
    
        private boolean isMatch(String query, String pattern) {
            int i = 0;
            for (char c: query.toCharArray()) {
                if (i < pattern.length() && c == pattern.charAt(i))
                    i++;
                else if (c < 'a')
                    return false;
            }
            return i == pattern.length();
        }
    

**C++**

    
    
         vector<bool> camelMatch(vector<string>& queries, string pattern) {
            vector<bool> res;
            for (string &query : queries) res.push_back(isMatch(query, pattern));
            return res;
    
        }
    
        bool isMatch(string query, string pattern) {
            int i = 0;
            for (char & c : query)
                if (i < pattern.length() && c == pattern[i]) i++;
                else if (c < 'a') return false;
            return i == pattern.length();
        }
    

**Python:**

    
    
         def camelMatch(self, qs, p):
            def u(s):
                return [c for c in s if c.isupper()]
    
            def issup(s, t):
                it = iter(t)
                return all(c in it for c in s)
            return [u(p) == u(q) and issup(p, q) for q in qs]
    

## **Solution 2: Use Regax**

Join `"[a-z]*"` into `pattern` characters.  
Slow compared with solution 1.

**Java**

    
    
        public List<Boolean> camel Match(String[] queries, String pattern) {;
            return Arrays.stream(queries).map(q -> q.matches("[a-z]*" + String.join("[a-z]*", pattern.split("")) + "[a-z]*")).collect(Collectors.toList());
        }
    

**Python**

    
    
         def camelMatch(self, qs, p):
            return [re.match("^[a-z]*" + "[a-z]*".join(p) + "[a-z]*$", q) != None for q in qs]
    

**C++**

    
    
         vector<bool> camelMatch(vector<string>& queries, string pattern) {
            vector<bool> res;
            string p = "[a-z]*";
            for (char &c: pattern)
                p += string(1, c) + "[a-z]*";
            regex r(p);
            for (string &query : queries)
                res.push_back(regex_match(query, r) );
            return res;
        }
    


### Solution 3
# Solution 1, Find

For each query, find all letters in `pattern` left-to-right. If we found all
pattern letters, check that the rest of the letters is in the lower case.

For simplicity, we can replace the found pattern letter in query with a
lowercase 'a'.

    
    
    vector<bool> camelMatch(vector<string>& qs, string pattern, vector<bool> res = {}) {
      for (auto i = 0; i < qs.size(); ++i) {
        for (auto p = -1, j = 0; j < pattern.size(); ++j) {
          p = qs[i].find(pattern[j], p + 1);
          if (p == string::npos) {
            res.push_back(false);
            break;
          }
          qs[i][p] = 'a';
        }
        if (res.size() <= i) res.push_back(all_of(begin(qs[i]), end(qs[i]), [](char ch) { return islower(ch); }));
      }
      return res;
    }
    

# Solution 2, Simple Scan

Instead of using the find function, we can just check all characters in the
query. If a character matches the pattern pointer (`pattern[p]`), we advance
that pointer (`++p`). Otherwise, we check that the query character is in the
lower case.

With this solution, it's also easer to realize that the complexity is O(n),
where n is the total number of query characters.

    
    
    vector<bool> camelMatch(vector<string>& qs, string pattern, vector<bool> res = {}) {
      for (auto i = 0, j = 0, p = 0; i < qs.size(); ++i) {
        for (j = 0, p = 0; j < qs[i].size(); ++j) {
          if (p < pattern.size() && qs[i][j] == pattern[p]) ++p;
          else if (!islower(qs[i][j])) break;
        }
        res.push_back(j == qs[i].size() && p == pattern.size());
      }
      return res;
    }
    

# Complexity Analysis

Runtime: _O(n)_ , where _n_ is all letters in all queries. We process each
letter only once.  
Memory: _O(m)_ , where _m_ is the number of queries (to store the result).



