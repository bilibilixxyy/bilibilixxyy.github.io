---
layout: post
title: 167. Two Sum II - Input array is sorted
---
### Question
Given an array of integers that is already **_sorted in ascending order_** ,
find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they
add up to the target, where index1 must be less than index2.

 **Note:**

  * Your returned answers (both index1 and index2) are not zero-based.
  * You may assume that each input would have _exactly_ one solution and you may not use the _same_ element twice.

 **Example:**

    
    
     **Input:** numbers = [2,7,11,15], target = 9
    **Output:** [1,2]
    **Explanation:** The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.

### Solution 1
Without HashMap, just have two pointers, A points to index 0, B points to
index len - 1, shrink the scope based on the value and target comparison.

    
    
    public int[] twoSum(int[] num, int target) {
        int[] indice = new int[2];
        if (num == null || num.length < 2) return indice;
        int left = 0, right = num.length - 1;
        while (left < right) {
            int v = num[left] + num[right];
            if (v == target) {
                indice[0] = left + 1;
                indice[1] = right + 1;
                break;
            } else if (v > target) {
                right --;
            } else {
                left ++;
            }
        }
        return indice;
    }


### Solution 2
    
    
    # two-pointer
    def twoSum1(self, numbers, target):
        l, r = 0, len(numbers)-1
        while l < r:
            s = numbers[l] + numbers[r]
            if s == target:
                return [l+1, r+1]
            elif s < target:
                l += 1
            else:
                r -= 1
     
    # dictionary           
    def twoSum2(self, numbers, target):
        dic = {}
        for i, num in enumerate(numbers):
            if target-num in dic:
                return [dic[target-num]+1, i+1]
            dic[num] = i
     
    # binary search        
    def twoSum(self, numbers, target):
        for i in xrange(len(numbers)):
            l, r = i+1, len(numbers)-1
            tmp = target - numbers[i]
            while l <= r:
                mid = l + (r-l)//2
                if numbers[mid] == tmp:
                    return [i+1, mid+1]
                elif numbers[mid] < tmp:
                    l = mid+1
                else:
                    r = mid-1


### Solution 3
We only have to shrink the range to find the pair:

class Solution {

public:

    
    
    vector<int> twoSum(vector<int>& numbers, int target) {
        int lo=0, hi=numbers.size()-1;
        while (numbers[lo]+numbers[hi]!=target){
            if (numbers[lo]+numbers[hi]<target){
                lo++;
            } else {
                hi--;
            }
        }
        return vector<int>({lo+1,hi+1});
    }
    

};



