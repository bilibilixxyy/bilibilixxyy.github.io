---
layout: post
title: 982. Minimum Increment to Make Array Unique
---
### Question
Given an array of integers A, a _move_ consists of choosing any `A[i]`, and
incrementing it by `1`.

Return the least number of moves to make every value in `A` unique.



 **Example 1:**

    
    
     **Input:** [1,2,2]
    **Output:** 1
    **Explanation:** After 1 move, the array could be [1, 2, 3].
    

**Example 2:**

    
    
    **Input:** [3,2,1,2,1,7]
    **Output:** 6
    **Explanation:** After 6 moves, the array could be [3, 4, 1, 2, 5, 7].
    It can be shown with 5 or less moves that it is impossible for the array to have all unique values.
    



 **Note:**

  1. `0 <= A.length <= 40000`
  2. `0 <= A[i] < 40000`

### Solution 1
##  **Solution 1: Just Sort, O(NlogN)**

Sort the input array.  
Compared with previous number,  
the current number need to be at least prev + 1.

Time Complexity: `O(NlogN)` for sorting  
Space: `O(1)` for in-space sort

Note that you can apply "O(N)" sort in sacrifice of space.  
Here we don't talk further about sort complexity.

 **C++:**

    
    
         int minIncrementForUnique(vector<int>& A) {
            sort(A.begin(), A.end());
            int res = 0, need = 0;
            for (int a: A) {
                res += max(need - a, 0);
                need = max(a, need)+1;
            }
            return res;
        }
    

**Java:**

    
    
         public int minIncrementForUnique(int[] A) {
            Arrays.sort(A);
            int res = 0, need = 0;
            for (int a : A) {
                res += Math.max(need - a, 0);
                need = Math.max(a, need) + 1;
            }
            return res;
        }
    

**Python:**

    
    
        def minIncrementForUnique(self, A):
            res = need =  0
            for i in sorted(A):
                res += max(need - i, 0)
                need = max(need + 1, i + 1)
            return res
    

  

## **Solution 2, O(KlogK)**

Same idea as solution 1 above.  
But instead of assign value one by one,  
we count the input numbers first, and assign values to all same value at one
time.

This solution has only `O(N)` time for cases like `[1,1,1,1,1,1,.....]`

Time Complexity:  
`O(NlogK)` using TreeMap in C++/Java  
`O(N + KlogK)` using HashMap in Python  
Space: `O(K)` for in-space sort

**C++:**

    
    
         int minIncrementForUnique(vector<int>& A) {
            map<int,int> count;
            for (int a : A) count[a]++;
            int res = 0, need = 0;
            for (auto x: count) {
                res += x.second * max(need - x.first, 0) + x.second * (x.second - 1) / 2;
                need = max(need, x.first) + x.second;
            }
            return res;
        }
    

**Java:**

    
    
         public int minIncrementForUnique(int[] A) {
            TreeMap<Integer, Integer> count = new TreeMap<>();
            for (int a : A) count.put(a, count.getOrDefault(a, 0) + 1);
            int res = 0, need = 0;
            for (int x: count.keySet()) {
                int v = count.get(x);
                res += v * Math.max(need - x, 0) + v * (v - 1) / 2;
                need = Math.max(need, x) + v;
            }
            return res;
        }
    

**Python:**

    
    
        def minIncrementForUnique(self, A):
             c = collections.Counter(A)
            res = need = 0
            for x in sorted(c):
                res += c[x] * max(need - x, 0) + c[x] * (c[x] - 1) / 2
                need = max(need, x) + c[x]
            return res
    

  

## **Solution 3: Union Find, O(N)**

Time: average `O(N)`  
Space: `O(N)`

**C++**

    
    
        unordered_map<int, int> root;
        int minIncrementForUnique(vector<int>& A) {
            int res =  0;
            for (int a : A)
                res += find(a) - a;
            return res;
        }
        int find(int x) {
            if (!root.count(x))
                root[x] = x;
            else if (x != root[x])
                root[x] = find(root[x]);
            else if (root.count(x + 1))
                root[x] = find(root[x + 1]);
            else
                root[x] = root[x + 1] = x + 1;
            return root[x];
        }
    

**Python**

    
    
        def minIncrementForUnique(self, A):
            root = {}
            def find( x):
                if x not in root:
                    root[x] = x
                elif x != root[x]:
                    root[x] = find(root[x])
                elif x + 1 in root:
                    root[x] = find(root[x + 1])
                else:
                    root[x] = root[x + 1] = x + 1
                return root[x]
            return sum(find(a) - a for a in A)
    


### Solution 2
The idea is to sort the input -O(nlogn)- , then we move forward from the
beginning of the array till the end.  
As soon as we found a condition that the current element is less than or equal
to the previous elements then we need to update the current array element.  
here is an example of the given input.  
A = [3,2,1,2,1,7]  
Sorted A = [1, **1** ,2,2,3,7]  
After reaching the second 1 on the array since the condition is satisfied
A[i]<=A[i-1] so we need to update the A[i] by A[i-1]+1.  
At the same time we need to keep track of result by  
result += A[i-1]+ 1 - A[i];

The rest of iterations are as following :

A = [1,2, **2** ,2,3,7]  
res= 1

A = [1,2,3, **2** ,3,7]  
res= 2

A = [1,2,3,4, **3** ,7]  
res= 4

A = [1,2,3,4,5, **7** ]  
res= 6

    
    
    class Solution {
    public:
        int minIncrementForUnique(vector<int>& A) {
            int s = A.size();
            int res=0;
            if (s<2)  return 0;
            sort(A.begin(),A.end());        
            for (int i=1; i<s; ++i) {
                if (A[i]<=A[i-1]){
                    res+=A[i-1]+1 -A[i];
                    A[i]= A[i-1]+1;
                }
            }
            return res;
        }
    };
    


### Solution 3
    
    
        public int minIncrementForUnique(int[] A) {
            int[] count = new int[40002];
            int result = 0;
            int max = 0;
            for (int a : A) {
                count[a]++;
                max = Math.max(max, a);
            }
            for (int i = 0; i < max; i++) {
                if (count[i] <= 1) {
                    continue;
                }
                int diff = count[i] - 1;
                result += diff;
                count[i + 1] += diff;
                count[i] = 1;
            }
            int diff = count[max] - 1;
            result += (1 + diff) * diff / 2;
            return result;
        }
    

Where `n` is the size of input, `m` is the largest number of input with the
maximize of 40,000.  
Use Arithmetic progression to count the final result.



