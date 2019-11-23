---
layout: post
title: 522. Longest Uncommon Subsequence II
---
### Question
Given a list of strings, you need to find the longest uncommon subsequence
among them. The longest uncommon subsequence is defined as the longest
subsequence of one of these strings and this subsequence should not be **any**
subsequence of the other strings.

A **subsequence** is a sequence that can be derived from one sequence by
deleting some characters without changing the order of the remaining elements.
Trivially, any string is a subsequence of itself and an empty string is a
subsequence of any string.

The input will be a list of strings, and the output needs to be the length of
the longest uncommon subsequence. If the longest uncommon subsequence doesn't
exist, return -1.

 **Example 1:**  

    
    
     **Input:** "aba", "cdc", "eae"
    **Output:** 3
    

**Note:**

  1. All the given strings' lengths will not exceed 10.
  2. The length of the given list will be in the range of [2, 50].

### Solution 1
Sort the strings in the reverse order. If there is not duplicates in the
array, then the longest string is the answer.

But if there are duplicates, and if the longest string is not the answer, then
we need to check other strings. But the smaller strings can be subsequence of
the bigger strings.  
For this reason, we need to check if the string is a subsequence of all the
strings bigger than itself. If it's not, that is the answer.

    
    
        public int findLUSlength(String[] strs) {
            Arrays.sort(strs, new Comparator<String>() {
                public int compare(String o1, String o2) {
                    return o2.length() - o1.length();
                }
            });
            
            Set<String> duplicates = getDuplicates(strs);
            for(int i = 0; i < strs.length; i++) {
                if(!duplicates.contains(strs[i])) {
                    if(i == 0) return strs[0].length();
                    for(int j = 0; j < i; j++) {
                        if(isSubsequence(strs[j], strs[i])) break;
                        if(j == i-1) return strs[i].length();
                    }
                }
            }
            return -1;
        }
        
        public boolean isSubsequence(String a, String b) {
            int i = 0, j = 0;
            while(i < a.length() && j < b.length()) {
                if(a.charAt(i) == b.charAt(j)) j++;
                i++;
            }
            return j == b.length();
        }
        
        private Set<String> getDuplicates(String[] strs) {
            Set<String> set = new HashSet<String>();
            Set<String> duplicates = new HashSet<String>();
            for(String s : strs) {
                if(set.contains(s)) duplicates.add(s);
                set.add(s);
            }
            return duplicates;
        }
    


### Solution 2
When we add a letter Y to our candidate longest uncommon subsequence answer of
X, it only makes it strictly harder to find a common subsequence. Thus our
candidate longest uncommon subsequences will be chosen from the group of words
itself.

Suppose we have some candidate X. We only need to check whether X is not a
subsequence of any of the other words Y. To save some time, we could have
quickly ruled out Y when len(Y) < len(X), either by adding "if len(w1) >
len(w2): return False" or enumerating over A[:i] (and checking neighbors for
equality.) However, the problem has such small input constraints that this is
not required.

We want the max length of all candidates with the desired property, so we
check candidates in descending order of length. When we find a suitable one,
we know it must be the best global answer.

    
    
    def subseq(w1, w2):
        #True iff word1 is a subsequence of word2.
        i = 0
        for c in w2:
            if i < len(w1) and w1[i] == c:
                i += 1
        return i == len(w1)
        
    A.sort(key = len, reverse = True)
    for i, word1 in enumerate(A):
        if all(not subseq(word1, word2) 
                for j, word2 in enumerate(A) if i != j):
            return len(word1)
    return -1
    


### Solution 3
We simply maintain a map of all subsequence frequencies and get the
subsequence with frequency 1 that has longest length.

NOTE: This solution does not take advantage of the fact that the optimal
length subsequence (if it exists) is always going to be the length of some
string in the array. Thus, the time complexity of this solution is non-
optimal. See <https://discuss.leetcode.com/topic/85044/python-simple-
explanation> for optimal solution.

    
    
    public int findLUSlength(String[] strs) {
        Map<String, Integer> subseqFreq = new HashMap<>();
        for (String s : strs) 
            for (String subSeq : getSubseqs(s))
                subseqFreq.put(subSeq, subseqFreq.getOrDefault(subSeq, 0) + 1);
        int longest = -1;
        for (Map.Entry<String, Integer> entry : subseqFreq.entrySet()) 
            if (entry.getValue() == 1) longest = Math.max(longest, entry.getKey().length());
        return longest;
    }
    
    public static Set<String> getSubseqs(String s) {
        Set<String> res = new HashSet<>();
        if (s.length() == 0) {
             res.add("");
             return res;
        }
        Set<String> subRes = getSubseqs(s.substring(1));
        res.addAll(subRes);
        for (String seq : subRes) res.add(s.charAt(0) + seq);
        return res;
    }
    



