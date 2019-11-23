---
layout: post
title: 1027. Sum of Even Numbers After Queries
---
### Question
We have an array `A` of integers, and an array `queries` of queries.

For the `i`-th query `val = queries[i][0], index = queries[i][1]`, we add val
to `A[index]`.  Then, the answer to the `i`-th query is the sum of the even
values of `A`.

 _(Here, the given`index = queries[i][1]` is a 0-based index, and each query
permanently modifies the array `A`.)_

Return the answer to all queries.  Your `answer` array should have `answer[i]`
as the answer to the `i`-th query.



 **Example 1:**

    
    
     **Input:** A = [1,2,3,4], queries = [[1,0],[-3,1],[-4,0],[2,3]]
    **Output:** [8,6,2,4]
    **Explanation:**
    At the beginning, the array is [1,2,3,4].
    After adding 1 to A[0], the array is [2,2,3,4], and the sum of even values is 2 + 2 + 4 = 8.
    After adding -3 to A[1], the array is [2,-1,3,4], and the sum of even values is 2 + 4 = 6.
    After adding -4 to A[0], the array is [-2,-1,3,4], and the sum of even values is -2 + 4 = 2.
    After adding 2 to A[3], the array is [-2,-1,3,6], and the sum of even values is -2 + 6 = 4.
    



 **Note:**

  1. `1 <= A.length <= 10000`
  2. `-10000 <= A[i] <= 10000`
  3. `1 <= queries.length <= 10000`
  4. `-10000 <= queries[i][0] <= 10000`
  5. `0 <= queries[i][1] < A.length`

### Solution 1
 **Track sum of all even #s.**  
There are 4 cases for odd / even property of A[k] and queries[i][0], where k =
queries[i][1]:  
1). **even** and odd, lose an even item in A; `sum` need to **deduct A[k]** ;  
2). **even** and even, get a **bigger even item in A** ; `sum` need to add
queries[i][0](same as **deduct A[k]** first then **add both** );  
3). odd and odd, get a **bigger even item in A** ; `sum` need to **add both**
;  
4). odd and even, no influence on even items in A;

Therefore, we can simplify the above as following procedure:

  1. find `sum` of all even #s;
  2. for each queries, check the item in A and after-added-up value, if  
a) the item in A is even, deduct it from `sum`; according to 1) & 2).  
b) after-added-up we have an even value, then add the new value to `sum`;
according to 2) & 3).

    
        public int[] sumEvenAfterQueries(int[] A, int[][] queries) {
        int sum = 0, i = 0;
        for (int a : A) { if (a % 2 == 0) sum += a ; } // sum of even #s.
        int[] ans = new int[queries.length];
        for (int[] q : queries) {
            if (A[q[1]] % 2 == 0) { sum -= A[q[1]]; } // from 1) and 2)
            A[q[1]] += q[0];
            if (A[q[1]] % 2 == 0) { sum += A[q[1]]; } // from 2) and 3)
            ans[i++] = sum;
        }
        return ans;
    }
    

**Analysis:**

 **Time: O(max(m, n)), space: O(n)** , where m = A.length, n = queries.length.


### Solution 2
First, we calculate `sum` of even numbers. Then, for each query, if `A[index]`
is even, we subtract its value from `sum`. Then we add `val` to `A[index]`. If
`A[index]` is now even, we add its value to `sum`.

    
    
    vector<int> sumEvenAfterQueries(vector<int>& A, vector<vector<int>>& qs, vector<int> res = {}) {
      int sum = accumulate(begin(A), end(A), 0, [](int s, int a) { return s + (a % 2 == 0 ? a : 0); });
      for (auto &q : qs) {
        if (A[q[1]] % 2 == 0) sum -= A[q[1]];
        A[q[1]] += q[0];
        if (A[q[1]] % 2 == 0) sum += A[q[1]];
        res.push_back(sum);
      }
      return res;
    }
    


### Solution 3
  1. Compute total as sum of all even values.
  2. For each query, record prev value at query position. Modify the value at query position.
  3. Adjust the total by subtracting prev if even and adding modified value if even.

    
    
    def sumEvenAfterQueries(self, A: 'List[int]', queries: 'List[List[int]]') -> 'List[int]':
            res=[]
            total=0
            for i in A:
                if i%2==0:
                    total+=i
            
            for val,idx in queries:
                prev=A[idx]
                A[idx]+=val
                if prev%2==0:
                    total-=prev
                if A[idx]%2==0:
                    total+=A[idx]
                res.append(total)
                
            return res
    



