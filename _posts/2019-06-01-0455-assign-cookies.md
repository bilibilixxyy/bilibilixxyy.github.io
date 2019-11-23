---
layout: post
title: 455. Assign Cookies
---
### Question
Assume you are an awesome parent and want to give your children some cookies.
But, you should give each child at most one cookie. Each child i has a greed
factor gi, which is the minimum size of a cookie that the child will be
content with; and each cookie j has a size sj. If sj >= gi, we can assign the
cookie j to the child i, and the child i will be content. Your goal is to
maximize the number of your content children and output the maximum number.

 **Note:**  
You may assume the greed factor is always positive.  
You cannot assign more than one cookie to one child.

 **Example 1:**  

    
    
     **Input:** [1,2,3], [1,1]
    
    **Output:** 1
    
    **Explanation:** You have 3 children and 2 cookies. The greed factors of 3 children are 1, 2, 3. 
    And even though you have 2 cookies, since their size is both 1, you could only make the child whose greed factor is 1 content.
    You need to output 1.
    

**Example 2:**  

    
    
    **Input:** [1,2], [1,2,3]
    
    **Output:** 2
    
    **Explanation:** You have 2 children and 3 cookies. The greed factors of 2 children are 1, 2. 
    You have 3 cookies and their sizes are big enough to gratify all of the children, 
    You need to output 2.
    

### Solution 1
    
    
    Arrays. sort(g);
    Arrays.sort(s);
    int i = 0;
    for(int j=0;i<g.length && j<s.length;j++) {
    	if(g[i]<=s[j]) i++;
    }
    return i;
    

Just assign the cookies starting from the child with less greediness to
maximize the number of happy children .


### Solution 2
Two assign cookies to children optimaly we should give for each child the
closest higher cookie. By using this greedy approach overall sum of wasted
cookies will be minimum amoung all. To use this greedy solution in effective
way we can sort both arrays and use two pointers. We should move pointer of
children only if there is enough cookies to make that child content. In each
step we will try to make content child at position pointerG by searching the
closes higher cookie value.

    
    
    public class Solution {
        public int findContentChildren(int[] g, int[] s) {
            Arrays.sort(g);
            Arrays.sort(s);
            
            int pointG = 0;
            int pointS = 0;
            
            while (pointG<g.length && pointS<s.length) {
                if (g[pointG]<=s[pointS]) {
                    pointG++;
                    pointS++;
                } else {
                    pointS++;
                }
            }
            
            return pointG;
        }
    }
    


### Solution 3
    
    
    class Solution(object):
        def findContentChildren(self, g, s):
            """
            :type g: List[int]
            :type s: List[int]
            :rtype: int
            """
            g.sort()
            s.sort()
            
            childi = 0
            cookiei = 0
            
            while cookiei < len(s) and childi < len(g):
                if s[cookiei] >= g[childi]:
                    childi += 1
                cookiei += 1
            
            return childi
                    
    



