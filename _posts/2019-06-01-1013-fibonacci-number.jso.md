---
layout: post
title: 1013. Fibonacci Number
---
### Question
The  **Fibonacci numbers** , commonly denoted `F(n)` form a sequence, called
the  **Fibonacci sequence** , such that each number is the sum of the two
preceding ones, starting from `0` and `1`. That is,

    
    
    F(0) = 0,   F(1) = 1
    F(N) = F(N - 1) + F(N - 2), for N > 1.
    

Given `N`, calculate `F(N)`.



**Example 1:**

    
    
    **Input:** 2
    **Output:** 1
    **Explanation:** F(2) = F(1) + F(0) = 1 + 0 = 1.
    

**Example 2:**

    
    
    **Input:** 3
    **Output:** 2
    **Explanation:** F(3) = F(2) + F(1) = 1 + 1 = 2.
    

**Example 3:**

    
    
    **Input:** 4
    **Output:** 3
    **Explanation:** F(4) = F(3) + F(2) = 2 + 1 = 3.
    



 **Note:**

0 ≤ `N` ≤ 30.

### Solution 1
 **Solution 1: Iterative**

Time complexity: `O(n)`  
Space complexity: `O(1)`

    
    
    class Solution 
    {
        public int fib(int N)
        {
            if(N <= 1)
                return N;
            
    		int a = 0, b = 1;
    		
    		while(N-- > 1)
    		{
    			int sum = a + b;
    			a = b;
    			b = sum;
    		}
            return b;
        }
    }
    

**Solution 2: Recursive**

Time complexity: `O(2^n)`\- since `T(n) = T(n-1) + T(n-2)`is an exponential
time  
Space complexity: `O(n)` \- space for recursive function call stack

    
    
    class Solution 
    {
        public int fib(int N)
        {
            if(N <= 1)
                return N;
            else
                return fib(N - 1) + fib(N - 2);
        }
    }
    

**Solution 3: Dynamic Programming - Top Down Approach**

Time complexity: `O(n)`  
Space complexity: `O(n)`

    
    
    class Solution 
    {
        int[] fib_cache = new int[31];
    	
    	public int fib(int N)
        {
            if(N <= 1)
                return N;
            else if(fib_cache[N] != 0)
                return fib_cache[N];
    		else 
                return fib_cache[N] = fib(N - 1) + fib(N - 2);
        }
    }
    

**Solution 4: Dynamic Programming - Bottom Up Approach**

Time complexity: `O(n)`  
Space complexity: `O(n)`

    
    
    class Solution 
    {
        public int fib(int N)
        {
            if(N <= 1)
                return N;
    
    		int[] fib_cache = new int[N + 1];
    		fib_cache[1] = 1;
    
    		for(int i = 2; i <= N; i++)
    		{
    			fib_cache[i] = fib_cache[i - 1] + fib_cache[i - 2];
    		}
    		return fib_cache[N];
        }
    }
    


### Solution 2
### Solution 1 - Recursive Approach

Time Complexity - O(2^N)  
Space Complexity - O(N) given the function call stack size

    
    
        int fib(int N) {
            if(N == 0)  return 0;
            if(N == 1)  return 1;
            return fib(N-1) + fib(N-2);
        }
    

### Solution 2 - Dynamic Programming Approach

Use memoization to store perviously computed fibonacci values.  
Time Complexity - O(N)  
Space Complexity - O(N)

    
    
        int fib(int N) {
            if(N < 2)
                return N;
            int memo[N+1];
            memo[0] = 0;
            memo[1] = 1;
            for(int i=2; i<=N; i++)
                memo[i] = memo[i-1] + memo[i-2];
            return memo[N];
        }
    

### Solution 3 - Imperative Approach (Bottom Up DP)

With Imperative approach, we step through the loop and optimize the space by
storing only two previous fibonacci values in two variables.  
Time Complexity - O(N)  
Space Complexity - O(1)

    
    
        int fib(int N) {
            if(N < 2) 
                return N;
        	int a = 0, b = 1, c = 0;
            for(int i = 1; i < N; i++)
            {
                c = a + b;
                a = b;
                b = c;
            }
            return c;
        }
    


### Solution 3
    
    
    class Solution:
        def fib(self, N):
            a,b = 0,1
            for _ in range(N):
                a, b = b, a+b
            return a 
    



