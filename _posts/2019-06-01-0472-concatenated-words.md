---
layout: post
title: 472. Concatenated Words
---
### Question
Given a list of words ( **without duplicates** ), please write a program that
returns all concatenated words in the given list of words.

A concatenated word is defined as a string that is comprised entirely of at
least two shorter words in the given array.

 **Example:**  

    
    
     **Input:** ["cat","cats","catsdogcats","dog","dogcatsdog","hippopotamuses","rat","ratcatdogcat"]
    
    **Output:** ["catsdogcats","dogcatsdog","ratcatdogcat"]
    
    **Explanation:** "catsdogcats" can be concatenated by "cats", "dog" and "cats";   
     "dogcatsdog" can be concatenated by "dog", "cats" and "dog";   
     "ratcatdogcat" can be concatenated by "rat", "cat", "dog" and "cat".
    

**Note:**  

  1. The number of elements of the given array will not exceed `10,000 `
  2. The length sum of elements in the given array will not exceed `600,000`. 
  3. All the input string will only include lower case letters.
  4. The returned elements order does not matter. 

### Solution 1
Do you still remember how did you solve this problem?
<https://leetcode.com/problems/word-break/>

If you do know one optimized solution for above question is using `DP`, this
problem is just one more step further. We iterate through each `word` and see
if it can be formed by using other `words`.

Of course it is also obvious that a `word` can only be formed by `words`
shorter than it. So we can first sort the input by length of each `word`, and
only try to form one `word` by using `words` in front of it.

    
    
    public class Solution {
        public static List<String> findAllConcatenatedWordsInADict(String[] words) {
            List<String> result = new ArrayList<>();
            Set<String> preWords = new HashSet<>();
            Arrays.sort(words, new Comparator<String>() {
                public int compare (String s1, String s2) {
                    return s1.length() - s2.length();
                }
            });
            
            for (int i = 0; i < words.length; i++) {
                if (canForm(words[i], preWords)) {
                    result.add(words[i]);
                }
                preWords.add(words[i]);
            }
            
            return result;
        }
    	
        private static boolean canForm(String word, Set<String> dict) {
            if (dict.isEmpty()) return false;
    	boolean[] dp = new boolean[word.length() + 1];
    	dp[0] = true;
    	for (int i = 1; i <= word.length(); i++) {
    	    for (int j = 0; j < i; j++) {
    		if (!dp[j]) continue;
    		if (dict.contains(word.substring(j, i))) {
    		    dp[i] = true;
    		    break;
    		}
    	    }
    	}
    	return dp[word.length()];
        }
    }
    


### Solution 2
    
    
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
            List<String> res = new ArrayList<String>();
            if (words == null || words.length == 0) {
                return res;
            }
            TrieNode root = new TrieNode();
            for (String word : words) { // construct Trie tree
                if (word.length() == 0) {
                    continue;
                }
                addWord(word, root);
            }
            for (String word : words) { // test word is a concatenated word or not
                if (word.length() == 0) {
                    continue;
                }
                if (testWord(word.toCharArray(), 0, root, 0)) {
                    res.add(word);
                }
            }
            return res;
        }
        public boolean testWord(char[] chars, int index, TrieNode root, int count) { // count means how many words during the search path
            TrieNode cur = root;
            int n = chars.length;
            for (int i = index; i < n; i++) {
                if (cur.sons[chars[i] - 'a'] == null) {
                    return false;
                }
                if (cur.sons[chars[i] - 'a'].isEnd) {
                    if (i == n - 1) { // no next word, so test count to get result.
                        return count >= 1;
                    }
                    if (testWord(chars, i + 1, root, count + 1)) {
                        return true;
                    }
                }
                cur = cur.sons[chars[i] - 'a'];
            }
            return false;
        }
        public void addWord(String str, TrieNode root) {
            char[] chars = str.toCharArray();
            TrieNode cur = root;
            for (char c : chars) {
                if (cur.sons[c - 'a'] == null) {
                    cur.sons[c - 'a'] = new TrieNode();
                }
                cur = cur.sons[c - 'a'];
            }
            cur.isEnd = true;
        }
    }
    class TrieNode {
        TrieNode[] sons;
        boolean isEnd;
        public TrieNode() {
            sons = new TrieNode[26];
        }
    


### Solution 3
    
    
    class Solution(object):
        def findAllConcatenatedWordsInADict(self, words):
            """
            :type words: List[str]
            :rtype: List[str]
            """
            d = set(words)
            
            def dfs(word):
                for i in range(1, len(word)):
                    prefix = word[:i]
                    suffix = word[i:]
                    
                    if prefix in d and suffix in d:
                        return True
                    if prefix in d and dfs(suffix):
                        return True
                    if suffix in d and dfs(prefix):
                        return True
                
                return False
            
            res = []
            for word in words:
                if dfs(word):
                    res.append(word)
            
            return res
    



