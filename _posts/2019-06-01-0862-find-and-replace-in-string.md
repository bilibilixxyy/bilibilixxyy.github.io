---
layout: post
title: 862. Find And Replace in String
---
### Question
To some string `S`, we will perform some replacement operations that replace
groups of letters with new ones (not necessarily the same size).

Each replacement operation has `3` parameters: a starting index `i`, a source
word `x` and a target word `y`.  The rule is that if `x` starts at position
`i` in the **original** **string** **`S`** , then we will replace that
occurrence of `x` with `y`.  If not, we do nothing.

For example, if we have `S = "abcd"` and we have some replacement operation `i
= 2, x = "cd", y = "ffff"`, then because `"cd"` starts at position `2` in the
original string `S`, we will replace it with `"ffff"`.

Using another example on `S = "abcd"`, if we have both the replacement
operation `i = 0, x = "ab", y = "eee"`, as well as another replacement
operation `i = 2, x = "ec", y = "ffff"`, this second operation does nothing
because in the original string `S[2] = 'c'`, which doesn't match `x[0] = 'e'`.

All these operations occur simultaneously.  It's guaranteed that there won't
be any overlap in replacement: for example, `S = "abc", indexes = [0, 1],
sources = ["ab","bc"]` is not a valid test case.

 **Example 1:**

    
    
     **Input:** S = "abcd", indexes = [0,2], sources = ["a","cd"], targets = ["eee","ffff"]
    **Output:** "eeebffff"
    **Explanation:** "a" starts at index 0 in S, so it's replaced by "eee".
    "cd" starts at index 2 in S, so it's replaced by "ffff".
    

**Example 2:**

    
    
    **Input:** S = "abcd", indexes = [0,2], sources = ["ab","ec"], targets = ["eee","ffff"]
    **Output:** "eeecd"
    **Explanation:** "ab" starts at index 0 in S, so it's replaced by "eee". 
    "ec" doesn't starts at index 2 in the **original** S, so we do nothing.
    

Notes:

  1. `0 <= indexes.length = sources.length = targets.length <= 100`
  2. `0 < indexes[i] < S.length <= 1000`
  3. All characters in given inputs are lowercase letters.

### Solution 1
##  **Intuition** :

Verify equality of string and change it if necessay.  
The only thing we need take attention is that the string form sources and
targets can be different.  
Instead of record the length changement, I sort indexes and do it from right
to left.  
(Since `indexes.length <= 100` is really small)  
In this way, the different length won't bother us anymore.  
  

##  **Explanation** :

  1. Descending `indexes[]` with tracking its index.
  2. Verify equality of subring in `S` source and source.
  3. Replace `S` if necessay.  
  

##  **Time Complexity** :

`O(SN)`

Comments from @CanDong:  
Since there won't be any overlap in replacement,  
every character in S will be compared at most once.  
If using StringBuilder, it should be O(NlogN + S).  
  

 **C++:**

    
    
         string findReplaceString(string S, vector<int>& indexes, vector<string>& sources, vector<string>& targets) {
            vector<pair<int, int>> sorted;
            for (int i = 0 ; i < indexes.size(); i++)
                sorted.push_back({indexes[i], i});
            sort(sorted.rbegin(), sorted.rend());
            for (auto ind : sorted) {
                int i = ind.first;
                string s = sources[ind.second], t = targets[ind.second];
                if (S.substr(i, s.length()) == s)
                    S = S.substr(0, i) + t + S.substr(i + s.length());
            }
            return S;
        }
    

**Java:**

    
    
        public String findReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
            List<int[]> sorted = new ArrayList<>();
             for (int i = 0 ; i < indexes.length; i++) sorted.add(new int[]{indexes[i], i});
            Collections.sort(sorted, Comparator.comparing(i -> -i[0]));
            for (int[] ind: sorted) {
                int i = ind[0], j = ind[1];
                String s = sources[j], t = targets[j];
                if (S.substring(i, i + s.length()).equals(s)) S = S.substring(0, i) + t + S.substring(i + s.length());
            }
            return S;
        }
    

**Python:**

    
    
         def findReplaceString(self, S, indexes, sources, targets):
            for i, s, t in sorted(zip(indexes, sources, targets), reverse=True):
                S = S[:i] + t + S[i + len(s):] if S[i:i + len(s)] == s else S
            return S
    

**1-line Python:**

    
    
         def findReplaceString(self, S, indexes, sources, targets):
            return reduce(lambda S, (i, s, t): S[:i] + t + S[i + len(s):] if S[i:i + len(s)] == s else S, sorted(zip(indexes, sources, targets))[::-1], S)
    


### Solution 2
The technique is like "piece table", which is used in editors. We record all
the valid operations first and put them into a piece table, then iterate the
string index to "apply" these operations.

    
    
        public String findReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
            Map<Integer, Integer> table = new HashMap<>();
            for (int i=0; i<indexes.length; i++) {
                // if a match is found in the original string, record it
                if (S.startsWith(sources[i], indexes[i])) {
                    table.put(indexes[i], i);
                }
            }
            StringBuilder sb = new StringBuilder();
            for (int i=0; i<S.length(); ) {
                if (table.containsKey(i)) {
                    // if a replacement was recorded before
                    sb.append(targets[table.get(i)]);
                    i+=sources[table.get(i)].length();
                } else {
                    // if no replacement happened at this index
                    sb.append(S.charAt(i));
                    i++;
                }
            }
            return sb.toString();
        }
    


### Solution 3
Method 1: Time O(nlogn), space: O(n).

1). Use **TreeMap** to sort _indexes_ : put the (indexes[i], i) pair into the
map;  
2). Iterate the **TreeMap** by reverse order, and hence avoid the change of
the indices of the rest part of the original String _S_ after each replace.

    
    
        public String find ReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
            StringBuilder sb = new StringBuilder(S);
            TreeMap<Integer, Integer> tm = new TreeMap<>();
            for (int i = 0; i < indexes.length; ++i) { tm.put(indexes[i], i); }
            for (int key : tm.descendingKeySet()) {
                int i = tm.get(key);
                if (S.substring(indexes[i]).startsWith(sources[i])) {
                    sb.replace(indexes[i], indexes[i] + sources[i].length(), targets[i]);
                }
            } 
            return sb.toString();
        }
    

Method 2: Time O(n), space: O(n).

No sort, use an array to store _indexes_ 's indices as values, values as
indices.

    
    
        public String find ReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
            StringBuilder sb = new StringBuilder(S);
            int len = S.length();
            int[] indexValInvert = new int[len];
            Arrays.fill(indexValInvert, -1);
            for (int i = 0; i < indexes.length; ++i) { indexValInvert[indexes[i]] = i; }
            for (int i = len - 1; i >= 0; --i) {
                int j = indexValInvert[i];
                if (j < 0) { continue; }
                if (S.substring(indexes[j]).startsWith(sources[j])) {
                    sb.replace(indexes[j], indexes[j] + sources[j].length(), targets[j]);
                }
            } 
            return sb.toString();
        }
    



