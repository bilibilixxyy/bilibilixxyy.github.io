---
layout: post
title: 1007. Numbers With Same Consecutive Differences
---
### Question
Return all **non-negative** integers of length `N` such that the absolute
difference between every two consecutive digits is `K`.

Note that **every** number in the answer **must not** have leading zeros
**except** for the number `0` itself. For example, `01` has one leading zero
and is invalid, but `0` is valid.

You may return the answer in any order.



 **Example 1:**

    
    
     **Input:** N = 3, K = 7
    **Output:** [181,292,707,818,929]
    **Explanation:** Note that 070 is not a valid number, because it has leading zeroes.
    

**Example 2:**

    
    
    **Input:** N = 2, K = 1
    **Output:** [10,12,21,23,32,34,43,45,54,56,65,67,76,78,87,89,98]



 **Note:**

  1. `1 <= N <= 9`
  2. `0 <= K <= 9`

### Solution 1
We initial the current result with all 1-digit numbers,  
like `cur = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]`.

Each turn, for each `x` in `cur`,  
we get its last digit `y = x % 10`.  
If `y + K < 10`, we add `x * 10 + y + K` to the new list.  
If `y - K >= 0`, we add `x * 10 + y - K` to the new list.

We repeat this step `N - 1` times and return the final result.

 **Java:**

    
    
         public int[] numsSameConsecDiff(int N, int K) {
            List<Integer> cur = Arrays.asList(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
            for (int i = 2; i <= N; ++i) {
                List<Integer> cur2 = new ArrayList<>();
                for (int x : cur) {
                    int y = x % 10;
                    if (x > 0 && y + K < 10)
                        cur2.add(x * 10 + y + K);
                    if (x > 0 && K > 0 && y - K >= 0)
                        cur2.add(x * 10 + y - K);
                }
                cur = cur2;
            }
            return cur.stream().mapToInt(j->j).toArray();
        }
    

**C++:**

    
    
        vector< int> numsSameConsecDiff(int N, int K) {
            vector<int> cur = {1, 2, 3, 4, 5, 6, 7, 8, 9, 0};
            for (int i = 2; i <= N; ++i) {
                vector<int> cur2;
                for (int x : cur) {
                    int y = x % 10;
                    if (x > 0 && y + K < 10)
                        cur2.push_back(x * 10 + y + K);
                    if (x > 0 && K > 0 && y - K >= 0)
                        cur2.push_back(x * 10 + y - K);
                }
                cur = cur2;
            }
            return cur;
        }
    

**Python:**

    
    
        def numsSameConsecDiff(self, N, K):
            cur = range( 10)
            for i in range(N - 1):
                cur = {x * 10 + y for x in cur for y in [x % 10 + K, x % 10 - K] if x and 0 <= y < 10}
            return list(cur)
    


### Solution 2
Idea -  
Keep adding digits from 0 to 9 to a temporary integer at units place. Before
adding we check if the digit being added is satisfying the condition of
differing with previous digit by K. We add untill the temporary integer has N
digits in it.

Start with the dfs function

  1. fill the base cases first.

  * If the Number has enough digits, add it to the final list.
  * When the temporary integer is empty add all the digits except 0.

  2. Later, fill the recursion for dfs.
  3. Think about handling edge cases - What should be returned if N = 0, N = 1 (where 0 should appear in end result) or K = 1 ?

    
    
    public int[] numsSameConsecDiff(int N, int K) {
            List<Integer> list = new ArrayList<>();
            if(N==0)
                return new int[0];
            if(N==1)
    			list.add(0);      // edge case
            dfs(N, K, list, 0);
            return list.stream().mapToInt(i->i).toArray();   //list.toArray(new int[list.size()]); doesn't work for primitives
        }
        public void dfs(int N, int K, List<Integer> list, int number){
            if(N == 0){   // base case, if you have added enough number of integers then append it to list; Here N is used as the total digits in temporary number 
                list.add(number);
                return ;
            }
            for(int i=0; i<10; ++i){
                if(i==0 && number ==0)    // Do not add 0 at begining of a number
                    continue;
                else if(number == 0 && i!=0){     // base case, we add all the digits when we do not have any previous digit to check if difference = K
                    dfs(N-1, K, list, i);
                }
                else{
                    if(Math.abs((number%10) - i )==K){
                        dfs(N-1, K, list, number*10+i);    // General dfs to add the digit at the units place and reducing the number of digits by 1.
                    }
                }
            }
        }
    


### Solution 3
Iterating for the first digit from 1 to 9, and building up the number from
here using DFS. The only strange situation here is when `N == 1`. The test
case creators think that the requirement 'the absolute difference between
every two consecutive digits is K' is true for any digit and any K if there
are no consecutive digits...

    
    
    void dfs(int num, int n, int K, vector<int> &res) {
      if (n == 0) res.push_back(num);
      else {
        auto dig = num % 10;
        if (dig + K <= 9) dfs(num * 10 + dig + K, n - 1, K, res);
        if (K != 0 && dig - K >= 0) dfs(num * 10 + dig - K, n - 1, K, res);
      }
    }
    vector<int> numsSameConsecDiff(int N, int K) {
      if (N == 1) return { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
      vector<int> res;
      for (auto num = 1; num <= 9; ++num) dfs(num, N - 1, K, res);
      return res;
    }
    



