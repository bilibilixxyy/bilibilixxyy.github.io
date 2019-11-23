---
layout: post
title: 950. X of a Kind in a Deck of Cards
---
### Question
In a deck of cards, each card has an integer written on it.

Return `true` if and only if you can choose `X >= 2` such that it is possible
to split the entire deck into 1 or more groups of cards, where:

  * Each group has exactly `X` cards.
  * All the cards in each group have the same integer.



 **Example 1:**

    
    
     **Input:** [1,2,3,4,4,3,2,1]
    **Output:** true
    **Explanation** : Possible partition [1,1],[2,2],[3,3],[4,4]
    

**Example 2:**

    
    
    **Input:** [1,1,1,2,2,2,3,3]
    **Output:** false
    **Explanation** : No possible partition.
    

**Example 3:**

    
    
    **Input:** [1]
    **Output:** false
    **Explanation** : No possible partition.
    

**Example 4:**

    
    
    **Input:** [1,1]
    **Output:** true
    **Explanation** : Possible partition [1,1]
    

**Example 5:**

    
    
    **Input:** [1,1,2,2,2,2]
    **Output:** true
    **Explanation** : Possible partition [1,1],[2,2],[2,2]
    

  
**Note:**

  1. `1 <= deck.length <= 10000`
  2. `0 <= deck[i] < 10000`

### Solution 1
Counts all occurrence of all numbers.  
Return if the greatest common divisor of counts > 1.

Time Complexity O(N).

 **C++:**

    
    
         bool hasGroupsSizeX(vector<int>& deck) {
            unordered_map<int, int> count;
            int res = 0;
            for (int i : deck) count[i]++;
            for (auto i : count) res = __gcd(i.second, res);
            return res > 1;
        }
    

**Java:**

    
    
         public boolean hasGroupsSizeX(int[] deck) {
            Map<Integer, Integer> count = new HashMap<>();
            int res = 0;
            for (int i : deck) count.put(i, count.getOrDefault(i, 0) + 1);
            for (int i : count.values()) res = gcd(i, res);
            return res > 1;
        }
    
        public int gcd(int a, int b) {
            return b > 0 ? gcd(b, a % b) : a;
        }
    

**Python:**

    
    
         def hasGroupsSizeX(self, deck):
            def gcd(a, b):
                while b: a, b = b, a % b
                return a
            count = collections.Counter(deck).values()
            return reduce(gcd, count) > 1
    

**Python 1-line**  
Idea from @gsk694

    
    
         def hasGroupsSizeX(self, deck):
            return reduce(fractions.gcd, collections.Counter(deck).values()) > 1
    


### Solution 2
这个题的基本思路是gcd， 但是这个题目的高分答案用了reduce隐藏了一种可能会出错的case，
(用reduce的都没错，不用reduce的很多code有问题)：  
deck = [0,0,0,0,0,0,0,0,2,2,2,2,2,2,2,2,2,1,1,1,1,1,1]  
所以我看到很多人为了不用reduce，然后直接找了dic或collection里面的最小值，用最小值来对比是否出现最大公约数，这实际上是错误的！为什么呢？
如上test case， 0： 8， 1:6， 2： 9， 用gcd 走gcd（6,8）和gcd（6,9）出来的结果都是大于1的，所以很多code直接就返了
True，但这个case应该返回False。  
This code is free of any import. It also takes care of the special case: deck
= [0,0,0,0,0,0,0,0,2,2,2,2,2,2,2,2,2,1,1,1,1,1,1,]. Right now this case is not
in the official test case list, but once it is in there, lots of high ranking
codes without using reduce will turn out to be wrong.

    
    
    class Solution:
        def hasGroupsSizeX(self, deck):
            def gcd(x, y):
                if y == 0 :
                    return x
                return gcd(y, x%y)
            dic = {}
            for card in deck:
                dic[card] = dic.get(card, 0) + 1
            values = list(dic.values())
            gcdres = gcd(values[0], values[0])
            for i in range(1, len(values)):
                gcdres = gcd(gcdres, values[i])
            return gcdres > 1
    


### Solution 3
This program revolves around the common GCD (HCF) of the counts.  
if the GCD becomes 1, `return false;`  
else `return true;`

Code:

    
    
    class Solution {
       public boolean hasGroupsSizeX(int[] deck) {
            int[] count = new int[10000];       
            for(int i=0; i<deck.length; i++)
            {        	
            	count[deck[i]]++;      	       	
            }
            
            int gcd = count[0]; 
            for (int i = 1; i < count.length; i++)        	
                    gcd = gcd(count[i], gcd); 
            
            if(gcd<2)
            	return false;
                 
            return true;
        }
    	
    	public int gcd(int a, int b) 
        { 
            if (a == 0) 
                return b; 
            return gcd(b % a, a); 
        } 
    }
    



