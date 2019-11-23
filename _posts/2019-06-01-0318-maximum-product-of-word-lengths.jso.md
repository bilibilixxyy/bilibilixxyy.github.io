---
layout: post
title: 318. Maximum Product of Word Lengths
---
### Question
Given a string array `words`, find the maximum value of `length(word[i]) *
length(word[j])` where the two words do not share common letters. You may
assume that each word will contain only lower case letters. If no such two
words exist, return 0.

 **Example 1:**

    
    
     **Input:** ["abcw","baz","foo","bar","xtfn","abcdef"]
    **Output:**16 
    **Explanation:**The two words can be "abcw", "xtfn".

**Example 2:**

    
    
    **Input:** ["a","ab","abc","d","cd","bcd","abcd"]
    **Output:**4 
    **Explanation:**The two words can be "ab", "cd".

**Example 3:**

    
    
    **Input:** ["a","aa","aaa","aaaa"]
    **Output:**0 
    **Explanation:**No such pair of words.
    

### Solution 1
    
    
    	public static int maxProduct(String[] words) {
    	 if (words == null || words.length == 0)
    		return 0;
    	int len = words.length;
    	int[] value = new int[len];
    	for (int i = 0; i < len; i++) {
    		String tmp = words[i];
    		value[i] = 0;
    		for (int j = 0; j < tmp.length(); j++) {
    			value[i] |= 1 << (tmp.charAt(j) - 'a');
    		}
    	}
    	int maxProduct = 0;
    	for (int i = 0; i < len; i++)
    		for (int j = i + 1; j < len; j++) {
    			if ((value[i] & value[j]) == 0 && (words[i].length() * words[j].length() > maxProduct))
    				maxProduct = words[i].length() * words[j].length();
    		}
    	return maxProduct;
    }


### Solution 2
    
    
    public class Solution {
        public int maxProduct(String[] words) {
            int max = 0;
    
            Arrays.sort(words, new Comparator<String>(){
                public int compare(String a, String b){
                    return b.length() - a.length();
                }
            });
    
            int[] masks = new int[words.length]; // alphabet masks
    
            for(int i = 0; i < masks.length; i++){
                for(char c: words[i].toCharArray()){
                    masks[i] |= 1 << (c - 'a');
                }
            }
        
            for(int i = 0; i < masks.length; i++){
                if(words[i].length() * words[i].length() <= max) break; //prunning
                for(int j = i + 1; j < masks.length; j++){
                    if((masks[i] & masks[j]) == 0){
                        max = Math.max(max, words[i].length() * words[j].length());
                        break; //prunning
                    }
                }
            }
    
            return max;
        }
    }


### Solution 3
Same algorithm as most, just written a bit shorter.

    
    
    int maxProduct(vector<string>& words) {
        vector<int> mask(words.size());
        int result = 0;
        for (int i=0; i<words.size(); ++i) {
            for (char c : words[i])
                mask[i] |= 1 << (c - 'a');
            for (int j=0; j<i; ++j)
                if (!(mask[i] & mask[j]))
                    result = max(result, int(words[i].size() * words[j].size()));
        }
        return result;
    }
    

**Update:** Here's an O(n+N) variation, where n is the number of words and N
is the total number of characters in all words. Thanks to junhuangli for the
suggestion.

    
    
     int maxProduct(vector<string>& words) {
        unordered_map<int,int> maxlen;
        for (string word : words) {
            int mask = 0;
            for (char c : word)
                mask |= 1 << (c - 'a');
            maxlen[mask] = max(maxlen[mask], (int) word.size());
        }
        int result = 0;
        for (auto a : maxlen)
            for (auto b : maxlen)
                if (!(a.first & b.first))
                    result = max(result, a.second * b.second);
        return result;
    }
    

**Or:** (thanks to junhuangli's further comment)

    
    
     int maxProduct(vector<string>& words) {
        unordered_map<int,int> maxlen;
        int result = 0;
        for (string word : words) {
            int mask = 0;
            for (char c : word)
                mask |= 1 << (c - 'a');
            maxlen[mask] = max(maxlen[mask], (int) word.size());
            for (auto maskAndLen : maxlen)
                if (!(mask & maskAndLen.first))
                    result = max(result, (int) word.size() * maskAndLen.second);
        }
        return result;
    }



