---
layout: post
title: 605. Can Place Flowers
---
### Question
Suppose you have a long flowerbed in which some of the plots are planted and
some are not. However, flowers cannot be planted in adjacent plots - they
would compete for water and both would die.

Given a flowerbed (represented as an array containing 0 and 1, where 0 means
empty and 1 means not empty), and a number **n** , return if **n** new flowers
can be planted in it without violating the no-adjacent-flowers rule.

 **Example 1:**  

    
    
     **Input:** flowerbed = [1,0,0,0,1], n = 1
    **Output:** True
    

**Example 2:**  

    
    
    **Input:** flowerbed = [1,0,0,0,1], n = 2
    **Output:** False
    

**Note:**  

  1. The input array won't violate no-adjacent-flowers rule.
  2. The input array size is in the range of [1, 20000].
  3.  **n** is a non-negative integer which won't exceed the input array size.

### Solution 1
Greedily place a flower at every vacant spot encountered from left to right!

    
    
    public class Solution {
        public boolean canPlaceFlowers(int[] flowerbed, int n) {
            int count = 0;
            for(int i = 0; i < flowerbed.length && count < n; i++) {
                if(flowerbed[i] == 0) {
    	     //get next and prev flower bed slot values. If i lies at the ends the next and prev are considered as 0. 
                   int next = (i == flowerbed.length - 1) ? 0 : flowerbed[i + 1]; 
                   int prev = (i == 0) ? 0 : flowerbed[i - 1];
                   if(next == 0 && prev == 0) {
                       flowerbed[i] = 1;
                       count++;
                   }
                }
            }
            
            return count == n;
        }
    }
    


### Solution 2
    
    
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        int count = 1;
        int result = 0;
        for(int i=0; i<flowerbed.length; i++) {
            if(flowerbed[i] == 0) {
                count++;
            }else {
                result += (count-1)/2;
                count = 0;
            }
        }
        if(count != 0) result += count/2;
        return result>=n;
    }


### Solution 3
    
    
    class Solution {
    public:
        bool canPlaceFlowers(vector<int>& flowerbed, int n) {
            flowerbed.insert(flowerbed.begin(),0);
            flowerbed.push_back(0);
            for(int i = 1; i < flowerbed.size()-1; ++i)
            {
                if(flowerbed[i-1] + flowerbed[i] + flowerbed[i+1] == 0)
                {
                    --n;
                    ++i;
                }
                    
            }
            return n <=0;
        }
    };
    



