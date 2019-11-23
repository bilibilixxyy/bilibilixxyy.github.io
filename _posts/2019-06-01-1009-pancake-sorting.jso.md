---
layout: post
title: 1009. Pancake Sorting
---
### Question
Given an array `A`, we can perform a  _pancake flip_ : We choose some positive
integer ` **k** <= A.length`, then reverse the order of the first **k**
elements of `A`.  We want to perform zero or more pancake flips (doing them
one after another in succession) to sort the array `A`.

Return the k-values corresponding to a sequence of pancake flips that sort
`A`.  Any valid answer that sorts the array within `10 * A.length` flips will
be judged as correct.



 **Example 1:**

    
    
     **Input:** [3,2,4,1]
    **Output:** [4,2,4,3]
    **Explanation:**
    We perform 4 pancake flips, with k values 4, 2, 4, and 3.
    Starting state: A = [3, 2, 4, 1]
    After 1st flip (k=4): A = [1, 4, 2, 3]
    After 2nd flip (k=2): A = [4, 1, 2, 3]
    After 3rd flip (k=4): A = [3, 2, 1, 4]
    After 4th flip (k=3): A = [1, 2, 3, 4], which is sorted. 
    

**Example 2:**

    
    
    **Input:** [1,2,3]
    **Output:** []
    **Explanation:** The input is already sorted, so there is no need to flip anything.
    Note that other answers, such as [3, 3], would also be accepted.
    



 **Note:**

  1. `1 <= A.length <= 100`
  2. `A[i]` is a permutation of `[1, 2, ..., A.length]`

### Solution 1
**Explanation**  
Find the index `i` of the next maximum number `x`.  
Reverse `i + 1` numbers, so that the `x` will be at `A[0]`  
Reverse `x` numbers, so that `x` will be at `A[x - 1]`.  
Repeat this process `N` times.

 **Update:**  
Actually, I didn't use the condition permutation of `[1,2,..., A.length]`.  
I searched in the descending order of `A`.

 **Time Complexity** :  
O(N^2)

  

 **Java:**

    
    
         public List<Integer> pancakeSort(int[] A) {
            List<Integer> res = new ArrayList<>();
            for (int x = A.length, i; x > 0; --x) {
                for (i = 0; A[i] != x; ++i);
                reverse(A, i + 1);
                res.add(i + 1);
                reverse(A, x);
                res.add(x);
            }
            return res;
        }
    
        public void reverse(int[] A, int k) {
            for (int i = 0, j = k - 1; i < j; i++, j--) {
                int tmp = A[i];
                A[i] = A[j];
                A[j] = tmp;
            }
        }
    

**C++:**

    
    
        vector< int> pancakeSort(vector<int> A) {
            vector<int> res;
            int x,i;
            for (x = A.size(); x > 0; --x) {
                for (i = 0; A[i] != x; ++i);
                reverse(A.begin(), A.begin() + i + 1);
                res.push_back(i + 1);
                reverse(A.begin(), A.begin() + x);
                res.push_back(x);
            }
            return res;
        }
    

**Python:**

    
    
         def pancakeSort(self, A):
            res = []
            for x in range(len(A), 1, -1):
                i = A.index(x)
                res.extend([i + 1, x])
                A = A[:i:-1] + A[:i]
            return res
    


### Solution 2
  1. Find the largest number
  2. Flip twice to the tail

Time: O(N^2)  
Flips: 2*N

    
    
    class Solution {
        public List<Integer> pancakeSort(int[] A) {
            List<Integer> result = new ArrayList<>();
            int n = A.length, largest = n;
            for (int i = 0; i < n; i++) {
                int index = find(A, largest);
                flip(A, index);
                flip(A, largest - 1);
                result.add(index + 1);
                result.add(largest--);
            }
            return result;
        }
        private int find(int[] A, int target) {
            for (int i = 0; i < A.length; i++) {
                if (A[i] == target) {
                    return i;
                }
            }
            return -1;
        }
        private void flip(int[] A, int index) {
            int i = 0, j = index;
            while (i < j) {
                int temp = A[i];
                A[i++] = A[j];
                A[j--] = temp;
            }
        }
    }
    


### Solution 3
Do A.size() times ,and every time find the max value of the A.size() - i cakes

    
    
    class Solution {
    public:
        vector<int> pancakeSort(vector<int>& A) {
            vector<int> result;
            for(int i = 0;i < A.size();i++){
               auto maxPosition = max_element(A.begin(), A.end() - i);
               result.push_back(maxPosition  - A.begin() + 1);  
               result.push_back(A.size() - i);
               reverse(A.begin(),maxPosition+1);
               reverse(A.begin(),A.end() - i);
            }
            return result;
        }
    };
    



