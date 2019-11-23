---
layout: post
title: 169. Majority Element
---
### Question
Given an array of size _n_ , find the majority element. The majority element
is the element that appears **more than** `⌊ n/2 ⌋` times.

You may assume that the array is non-empty and the majority element always
exist in the array.

 **Example 1:**

    
    
     **Input:** [3,2,3]
    **Output:** 3

**Example 2:**

    
    
    **Input:** [2,2,1,1,1,2,2]
    **Output:** 2
    

### Solution 1
    
    
     public class Solution {
        public int majorityElement(int[] num) {
    
            int major=num[0], count = 1;
            for(int i=1; i<num.length;i++){
                if(count==0){
                    count++;
                    major=num[i];
                }else if(major==num[i]){
                    count++;
                }else count--;
                
            }
            return major;
        }
    }


### Solution 2
 **Hash Table**  
Count the number of appearances for each distinct number in `nums`, once we
see a number appear more than `n / 2` times, it is the majority element.

    
    
    class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            unordered_map<int, int> counter;
            for (int num : nums) {
                if (++counter[num] > nums.size() / 2) {
                    return num;
                }
            }
            return 0;
        }
    };
    

**Sorting**  
Since the majority element appears more than `n / 2` times, the `n / 2`-th
element in the sorted `nums` must be the majority element. In this case, a
partial sort by `nth_element` is enough.

    
    
     class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            nth_element(nums.begin(), nums.begin() + nums.size() / 2, nums.end());
            return nums[nums.size() / 2];
        }
    };
    

**Randomization**  
Pick an element randomly and check whether it is the majority one.

    
    
     class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            int n = nums.size(), candidate, counter;
            srand(unsigned(time(NULL)));
            while (true) {
                candidate = nums[rand() % n], counter = 0;
                for (int num : nums) {
                    if (num == candidate) {
                        counter++;
                    }
                }
                if (counter > n / 2) {
                    break;
                }
            }
            return candidate;
        }
    };
    

**Divide and Conquer**  
Recursively find the majority in the two halves of `nums` and combine the
results. The base case is that the majority element of a single-element array
is just that element.

    
    
     class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            return majority(nums, 0, nums.size() - 1);
        }
    private:
        int majority(vector<int>& nums, int l, int r) {
            if (l == r) {
                return nums[l];
            }
            int m = l + (r - l) / 2, lm = majority(nums, l, m), rm = majority(nums, m + 1, r);
            if (lm == rm) {
                return lm;
            }
            return count(nums.begin() + l, nums.begin() + r + 1, lm) > count(nums.begin() + l, nums.begin() + r + 1, rm) ? lm : rm;
        }
    }; 
    

**Moore Voting Algorithm**

    
    
     class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            int counter = 0, majority;
            for (int num : nums) {
                if (!counter) {
                    majority = num;
                }
                counter += num == majority ? 1 : -1;
            }
            return majority;
        }
    };
    

**Bit Manipulation**  
The bits in the majority are just the majority bits of all numbers.

    
    
     class Solution {
    public:
        int majorityElement(vector<int>& nums) {
            int majority = 0;
            for (unsigned int i = 0, mask = 1; i < 32; i++, mask <<= 1) {
                int bits = 0;
                for (int num : nums) {
                    if (num & mask) {
                        bits++;
                    }
                }
                if (bits > nums.size() / 2) {
                    majority |= mask;
                }
            }
            return majority;
        }
    };
    


### Solution 3
    
    
    // Sorting
    public int majorityElement1(int[] nums) {
        Arrays.sort(nums);
        return nums[nums.length/2];
    }
    
    // Hashtable 
    public int majorityElement2(int[] nums) {
        Map<Integer, Integer> myMap = new HashMap<Integer, Integer>();
        //Hashtable<Integer, Integer> myMap = new Hashtable<Integer, Integer>();
        int ret=0;
        for (int num: nums) {
            if (!myMap.containsKey(num))
                myMap.put(num, 1);
            else
                myMap.put(num, myMap.get(num)+1);
            if (myMap.get(num)>nums.length/2) {
                ret = num;
                break;
            }
        }
        return ret;
    }
    
    // Moore voting algorithm
    public int majorityElement3(int[] nums) {
        int count=0, ret = 0;
        for (int num: nums) {
            if (count==0)
                ret = num;
            if (num!=ret)
                count--;
            else
                count++;
        }
        return ret;
    }
    
    // Bit manipulation 
    public int majorityElement(int[] nums) {
        int[] bit = new int[32];
        for (int num: nums)
            for (int i=0; i<32; i++) 
                if ((num>>(31-i) & 1) == 1)
                    bit[i]++;
        int ret=0;
        for (int i=0; i<32; i++) {
            bit[i]=bit[i]>nums.length/2?1:0;
            ret += bit[i]*(1<<(31-i));
        }
        return ret;
    }



