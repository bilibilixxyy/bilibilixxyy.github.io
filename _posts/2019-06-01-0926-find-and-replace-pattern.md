---
layout: post
title: 926. Find and Replace Pattern
---
### Question
You have a list of `words` and a `pattern`, and you want to know which words
in `words` matches the pattern.

A word matches the pattern if there exists a permutation of letters `p` so
that after replacing every letter `x` in the pattern with `p(x)`, we get the
desired word.

( _Recall that a permutation of letters is a bijection from letters to
letters: every letter maps to another letter, and no two letters map to the
same letter._ )

Return a list of the words in `words` that match the given pattern.

You may return the answer in any order.



 **Example 1:**

    
    
     **Input:** words = ["abc","deq","mee","aqq","dkd","ccc"], pattern = "abb"
    **Output:** ["mee","aqq"]
    **Explanation:** "mee" matches the pattern because there is a permutation {a -> m, b -> e, ...}. 
    "ccc" does not match the pattern because {a -> c, b -> c, ...} is not a permutation,
    since a and b map to the same letter.



 **Note:**

  * `1 <= words.length <= 50`
  * `1 <= pattern.length = words[i].length <= 20`

### Solution 1
Normalise a string to a standard pattern.  
Yes. You can pass the F(pattern) to the sub function and return earlier in
case of dismatch.

 **C++**

    
    
         vector<string> findAndReplacePattern(vector<string> words, string p) {
            vector<string> res;
            for (string w : words) if (F(w) == F(p)) res.push_back(w);
            return res;
        }
    
        string F(string w) {
            unordered_map<char, int> m;
            for (char c : w) if (!m.count(c)) m[c] = m.size();
            for (int i = 0; i < w.length(); ++i) w[i] = 'a' + m[w[i]];
            return w;
        }
    

**Java:**

    
    
         public List<String> findAndReplacePattern(String[] words, String pattern) {
            int[] p = F(pattern);
            List<String> res = new ArrayList<String>();
            for (String w : words)
                if (Arrays.equals(F(w), p)) res.add(w);
            return res;
        }
    
        public int[] F(String w) {
            HashMap<Character, Integer> m = new HashMap<>();
            int n = w.length();
            int[] res = new int[n];
            for (int i = 0; i < n; i++) {
                m.putIfAbsent(w.charAt(i), m.size());
                res[i] = m.get(w.charAt(i));
            }
            return res;
        }
    

**Python:**

    
    
         def findAndReplacePattern(self, words, p):
            def F(w):
                m = {}
                return [m.setdefault(c, len(m)) for c in w]
            return [w for w in words if F(w) == F(p)]
    


### Solution 2
    
    
    class Solution {
        public List<String> findAndReplacePattern(String[] words, String pattern) {
            List<String> res= new LinkedList<>();
            for (String w: words){
                int[] p= new int[26], s= new int[26];
                boolean same=true;
                for (int i=0; i<w.length(); i++){
                    if (s[w.charAt(i)-'a']!=p[pattern.charAt(i)-'a']){
                        same=false;
                        break;
                    }else{
                        s[w.charAt(i)-'a']=p[pattern.charAt(i)-'a']=i+1;
                    }
                }
                if (same) res.add(w);
            }
            return res;
        }
    }
    


### Solution 3
Similar to an earlier isomorphic string problem, check the length of the sets
and the length of the set when the characters are zipped together.

    
    
            b = pattern
            def is_iso(a):
                return len(a) == len(b) and len(set(a)) == len(set(b)) == len(set(zip(a, b)))
            return filter(is_iso, words)
    



