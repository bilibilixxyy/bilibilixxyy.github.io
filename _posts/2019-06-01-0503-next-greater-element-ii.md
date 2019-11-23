---
layout: post
title: 503. Next Greater Element II
---
### Question
Given a circular array (the next element of the last element is the first
element of the array), print the Next Greater Number for every element. The
Next Greater Number of a number x is the first greater number to its
traversing-order next in the array, which means you could search circularly to
find its next greater number. If it doesn't exist, output -1 for this number.

 **Example 1:**  

    
    
     **Input:** [1,2,1]
    **Output:** [2,-1,2]
    **Explanation:** The first 1's next greater number is 2;   
     The number 2 can't find next greater number;   
    The second 1's next greater number needs to search circularly, which is also 2.
    

**Note:** The length of given array won't exceed 10000.

### Solution 1
The approach is same as _Next Greater Element I_  
See explanation in [my solution to the previous
problem](https://discuss.leetcode.com/topic/77916/java-10-lines-linear-time-
complexity-o-n-with-explanation)  
The only difference here is that we use `stack` to keep the **indexes** of the
**decreasing** subsequence

 **Java**

    
    
         public int[] nextGreaterElements(int[] nums) {
            int n = nums.length, next[] = new int[n];
            Arrays.fill(next, -1);
            Stack<Integer> stack = new Stack<>(); // index stack
            for (int i = 0; i < n * 2; i++) {
                int num = nums[i % n]; 
                while (!stack.isEmpty() && nums[stack.peek()] < num)
                    next[stack.pop()] = num;
                if (i < n) stack.push(i);
            }   
            return next;
        }
    

**C++**

    
    
         vector<int> nextGreaterElements(vector<int>& nums) {
            int n = nums.size();
            vector<int> next(n, -1);
            stack<int> s; // index stack
            for (int i = 0; i < n * 2; i++) {
                int num = nums[i % n]; 
                while (!s.empty() && nums[s.top()] < num) {
                    next[s.top()] = num;
                    s.pop();
                }
                if (i < n) s.push(i);
            }   
            return next;
        }
    


### Solution 2
The first typical way to solve circular array problems is to extend the
original array to twice length, 2nd half has the same element as first half.
Then everything become simple.  
Naive by simple solution, just look for the next greater element directly.
Time complexity: O(n^2).

    
    
    public class Solution {
        public int[] nextGreaterElements(int[] nums) {
            int max = Integer.MIN_VALUE;
            for (int num : nums) {
                max = Math.max(max, num);
            }
            
            int n = nums.length;
            int[] result = new int[n];
            int[] temp = new int[n * 2];
            
            for (int i = 0; i < n * 2; i++) {
                temp[i] = nums[i % n];
            }
            
            for (int i = 0; i < n; i++) {
                result[i] = -1;
                if (nums[i] == max) continue;
                
                for (int j = i + 1; j < n * 2; j++) {
                    if (temp[j] > nums[i]) {
                        result[i] = temp[j];
                        break;
                    }
                }
            }
            
            return result;
        }
    }
    

The second way is to use a `stack` to facilitate the look up. First we put all
indexes into the stack, `smaller` index on the `top`. Then we start from `end`
of the array look for the first element (index) in the stack which is greater
than the current one. That one is guaranteed to be the `Next Greater Element`.
Then put the current element (index) into the stack.  
Time complexity: O(n).

    
    
    public class Solution {
        public int[] nextGreaterElements(int[] nums) {
            int n = nums.length;
            int[] result = new int[n];
            
            Stack<Integer> stack = new Stack<>();
            for (int i = n - 1; i >= 0; i--) {
                stack.push(i);
            }
            
            for (int i = n - 1; i >= 0; i--) {
                result[i] = -1;
                while (!stack.isEmpty() && nums[stack.peek()] <= nums[i]) {
                    stack.pop();
                }
                if (!stack.isEmpty()){
                    result[i] = nums[stack.peek()];
                }
                stack.add(i);
            }
            
            return result;
        }
    }
    


### Solution 3
Loop once, we can get the Next Greater Number of a normal array.  
Loop twice, we can get the Next Greater Number of a circular array

 **Java**

    
    
         public int[] nextGreaterElements(int[] A) {
            int n = A.length, res[] = new int[n];
            Arrays.fill(res, -1);
            Stack<Integer> stack = new Stack<>();
            for (int i = 0; i < n * 2; i++) {
                while (!stack.isEmpty() && A[stack.peek()] < A[i % n])
                    res[stack.pop()] = A[i % n];
                stack.push(i % n);
            }
            return res;
        }
    

**C++:**

    
    
         vector<int> nextGreaterElements(vector<int>& A) {
            int n = A.size();
            vector<int> stack, res(n,-1);
            for (int i = 0; i < n * 2; ++i) {
                while (stack.size() && A[stack.back()] < A[i%n]) {
                    res[stack.back()] = A[i%n];
                    stack.pop_back();
                }
                stack.push_back(i%n);
            }
            return res;
        }
    

**Python:**

    
    
        def nextGreaterElements(self, A):
            stack,  res = [], [-1] * len(A)
            for i in range(len(A)) * 2:
                while stack and (A[stack[-1]] < A[i]):
                    res[stack.pop()] = A[i]
                stack.append(i)
            return res
    



