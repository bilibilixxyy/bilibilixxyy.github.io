---
layout: post
title: 17. Letter Combinations of a Phone Number
---
### Question
Given a string containing digits from `2-9` inclusive, return all possible
letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given
below. Note that 1 does not map to any letters.

![](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-
keypad2.svg/200px-Telephone-keypad2.svg.png)

 **Example:**

    
    
     **Input:** "23"
    **Output:** ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
    

**Note:**

Although the above answer is in lexicographical order, your answer could be in
any order you want.

### Solution 1
    
    
     public List<String> letterCombinations(String digits) {
    		LinkedList<String> ans = new LinkedList<String>();
    		if(digits.isEmpty()) return ans;
    		String[] mapping = new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    		ans.add("");
    		for(int i =0; i<digits.length();i++){
    			int x = Character.getNumericValue(digits.charAt(i));
    			while(ans.peek().length()==i){
    				String t = ans.remove();
    				for(char s : mapping[x].toCharArray())
    					ans.add(t+s);
    			}
    		}
    		return ans;
    	}
    

A version without first loop, but same time complexity. Both are single queue
BFS solutions.:

    
    
    public List<String> letterCombinations(String digits) {
    		LinkedList<String> ans = new LinkedList<String>();
    		if(digits.isEmpty()) return ans;
    		String[] mapping = new String[] {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
    		ans.add("");
    		while(ans.peek().length()!=digits.length()){
    			String remove = ans.remove();
    			String map = mapping[digits.charAt(remove.length())-'0'];
    			for(char c: map.toCharArray()){
    				ans.addLast(remove+c);
    			}
    		}
    		return ans;
    	}
    


### Solution 2
This is my solution, FYI

    
    
    vector<string> letterCombinations(string digits) {
        vector<string> res;
        string charmap[10] = {"0", "1", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};
        res.push_back("");
        for (int i = 0; i < digits.size(); i++)
        {
            vector<string> tempres;
            string chars = charmap[digits[i] - '0'];
            for (int c = 0; c < chars.size();c++)
                for (int j = 0; j < res.size();j++)
                    tempres.push_back(res[j]+chars[c]);
            res = tempres;
        }
        return res;
    }


### Solution 3
    
    
       public class Solution {
        	private static final String[] KEYS = { "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz" };
        
        	public List<String> letterCombinations(String digits) {
        		List<String> ret = new LinkedList<String>();
        		combination("", digits, 0, ret);
        		return ret;
        	}
        
        	private void combination(String prefix, String digits, int offset, List<String> ret) {
        		if (offset >= digits.length()) {
        			ret.add(prefix);
        			return;
        		}
        		String letters = KEYS[(digits.charAt(offset) - '0')];
        		for (int i = 0; i < letters.length(); i++) {
        			combination(prefix + letters.charAt(i), digits, offset + 1, ret);
        		}
        	}
        }



