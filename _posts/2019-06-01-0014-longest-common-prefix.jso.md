---
layout: post
title: 14. Longest Common Prefix
---
### Question
Write a function to find the longest common prefix string amongst an array of
strings.

If there is no common prefix, return an empty string `""`.

 **Example 1:**

    
    
     **Input:** ["flower","flow","flight"]
    **Output:** "fl"
    

**Example 2:**

    
    
    **Input:** ["dog","racecar","car"]
    **Output:** ""
    **Explanation:** There is no common prefix among the input strings.
    

**Note:**

All given inputs are in lowercase letters `a-z`.

### Solution 1
    
    
     public String longestCommonPrefix(String[] strs) {
        if(strs == null || strs.length == 0)    return "";
        String pre = strs[0];
        int i = 1;
        while(i < strs.length){
            while(strs[i].indexOf(pre) != 0)
                pre = pre.substring(0,pre.length()-1);
            i++;
        }
        return pre;
    }


### Solution 2
Sort the array first, and then you can simply compare the first and last
elements in the sorted array.

    
    
        public String longestCommonPrefix(String[] strs) {
            StringBuilder result = new StringBuilder();
            
            if (strs!= null && strs.length > 0){
            
                Arrays.sort(strs);
                
                char [] a = strs[0].toCharArray();
                char [] b = strs[strs.length-1].toCharArray();
                
                for (int i = 0; i < a.length; i ++){
                    if (b.length > i && b[i] == a[i]){
                        result.append(b[i]);
                    }
                    else {
                        return result.toString();
                    }
                }
            return result.toString();
        }


### Solution 3
    
    
     def longestCommonPrefix(self, strs):
            """
            :type strs: List[str]
            :rtype: str
            """
            if not strs:
                return ""
            shortest = min(strs,key=len)
            for i, ch in enumerate(shortest):
                for other in strs:
                    if other[i] != ch:
                        return shortest[:i]
            return shortest 
    



