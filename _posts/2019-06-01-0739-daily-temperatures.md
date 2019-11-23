---
layout: post
title: 739. Daily Temperatures
---
### Question
Given a list of daily temperatures `T`, return a list such that, for each day
in the input, tells you how many days you would have to wait until a warmer
temperature. If there is no future day for which this is possible, put `0`
instead.

For example, given the list of temperatures `T = [73, 74, 75, 71, 69, 72, 76,
73]`, your output should be `[1, 1, 4, 2, 1, 1, 0, 0]`.

**Note:** The length of `temperatures` will be in the range `[1, 30000]`. Each
temperature will be an integer in the range `[30, 100]`.

### Solution 1
Time: O(N)  
Space: O(N)

Method 1. `Stack` (54 ms)

    
    
    public int[] dailyTemperatures(int[] temperatures) {
        Stack<Integer> stack = new Stack<>();
        int[] ret = new int[temperatures.length];
        for(int i = 0; i < temperatures.length; i++) {
            while(!stack.isEmpty() && temperatures[i] > temperatures[stack.peek()]) {
                int idx = stack.pop();
                ret[idx] = i - idx;
            }
            stack.push(i);
        }
        return ret;
    }
    

Method 2. `Array` (10 ms)

    
    
    public int[] dailyTemperatures(int[] temperatures) {
        int[] stack = new int[temperatures.length];
        int top = -1;
        int[] ret = new int[temperatures.length];
        for(int i = 0; i < temperatures.length; i++) {
            while(top > -1 && temperatures[i] > temperatures[stack[top]]) {
                int idx = stack[top--];
                ret[idx] = i - idx;
            }
            stack[++top] = i;
        }
        return ret;
    }
    


### Solution 2
Improved from the stack solution, which iterates backwards.  
We itereate forward here so that `enumerate()` can be used.  
Everytime a higher temperature is found, we update answer of the peak one in
the stack.  
If the day with higher temperature is not found, we leave the ans to be the
default `0`.

    
    
      def dailyTemperatures(self, T):
        ans = [0] * len(T)
        stack = []
        for i, t in enumerate(T):
          while stack and T[stack[-1]] < t:
            cur = stack.pop()
            ans[cur] = i - cur
          stack.append(i)
    
        return ans
    
    


### Solution 3
We compute the result in reverse order. Given `temperatures = [73, 74, 75, 71,
69, 72, 76, 73]`, suppose that we already obtain number of days for `i >= 3`:
`res = [?, ?, ?, 2, 1, 1, 0, 0]` where `?` denotes values to be determined. In
order to find the number of days for `i = 2`, we proceed as follows:

  1. Let `j = i + 1`, i.e., `j = 3`. If `temperatures[i] < temperatures[j]`, `res[i] = 1`;
  2. Otherwise, `temperatures[i] >= temperatures[j]`. we examine the value of `res[j]`, which is equal to `2`, which means that the most recent day with higher temperature than `temperatures[j]` in the future is `2` days away. Since `temperatures[i] >= temperatures[j]`, the most recent day with higher temperature than `temperatures[i]` should be at least `2` days away. Therefore, we can skip some days and arrive directly at day `j + res[j]`. This process continues until we find higher temperature or we know we should stop as descibed in the third point.
  3. If `temperatures[j] == 0`, we shoud directly set `res[i]` to `0` since we know we cannot find higher temperature in the future.

The code is as follows. The complexity is `O(n)` in time and `O(1)` in space
(since we leverage the space of the output).

    
    
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        vector<int> res(temperatures.size());
        for (int i = temperatures.size() - 1; i >= 0; --i) {
            int j = i+1;
            while (j < temperatures.size() && temperatures[j] <= temperatures[i]) {
                if (res[j] > 0) j = res[j] + j;
                else j = temperatures.size();
            }
            // either j == size || temperatures[j] > temperatures[i]
            if (j < temperatures.size()) res[i] = j - i;
        }
        return res;
    }
    



