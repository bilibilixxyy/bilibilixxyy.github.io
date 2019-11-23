---
layout: post
title: 992. Delete Columns to Make Sorted II
---
### Question
We are given an array `A` of `N` lowercase letter strings, all of the same
length.

Now, we may choose any set of deletion indices, and for each string, we delete
all the characters in those indices.

For example, if we have an array `A = ["abcdef","uvwxyz"]` and deletion
indices `{0, 2, 3}`, then the final array after deletions is `["bef","vyz"]`.

Suppose we chose a set of deletion indices `D` such that after deletions, the
final array has its elements in **lexicographic** order (`A[0] <= A[1] <= A[2]
... <= A[A.length - 1]`).

Return the minimum possible value of `D.length`.



 **Example 1:**

    
    
     **Input:** ["ca","bb","ac"]
    **Output:** 1
    **Explanation:**
    After deleting the first column, A = ["a", "b", "c"].
    Now A is in lexicographic order (ie. A[0]  <= A[1] <= A[2]).
    We require at least 1 deletion since initially A was not in lexicographic order, so the answer is 1.
    

**Example 2:**

    
    
    **Input:** ["xc","yb","za"]
    **Output:** 0
    **Explanation:**
    A is already in lexicographic order, so we don't need to delete anything.
    Note that the rows of A are not necessarily in lexicographic order:
    ie. it is NOT necessarily true that (A[0][0]  <= A[0][1] <= ...)
    

**Example 3:**

    
    
    **Input:** ["zyx","wvu","tsr"]
    **Output:** 3
    **Explanation:**
    We have to delete every column.
    



 **Note:**

  1. `1 <= A.length <= 100`
  2. `1 <= A[i].length <= 100`

### Solution 1
##  **Intuition**

Solve it with a greed algorithme.

Initial `N` empty string.  
For each column,  
add the character to each row,  
and check if all rows are still sorted.

If not, we have to delete this column.  
  

##  **Explanation**

Initial a boolean array `sorted`,  
`sorted[i] = true` if and only if `A[i] < A[i + 1]`,  
that is to say `A[i]` and `A[i + 1]` are sorted.

For each col, we check all unsorted rows.  
If `A[i][j] > A[i + 1][j]`, we need to deleted this col, `res++`.

Otherwise, we can keep this col, and update all sorted rows.  
  

 **Java, Using array, O(MN), 14ms**

    
    
        public int minDeletionSize(String[] A) {
            int res =  0, n = A.length, m = A[0].length(), i, j;
            boolean[] sorted = new boolean[n - 1];
            for (j = 0; j < m; ++j) {
                for (i = 0; i < n - 1; ++i) {
                    if (!sorted[i] && A[i].charAt(j) > A[i + 1].charAt(j)) {
                        res++;
                        break;
                    }
                }
                if (i < n - 1) continue;
                for (i = 0; i < n - 1; ++i)
                    if (A[i].charAt(j) < A[i + 1].charAt(j))
                        sorted[i] = true;
            }
            return res;
        }
    

**C++, Using vector, O(MN), 8ms**

    
    
        int minDeletionSize(vector<string>& A) {
            int res =  0, n = A.size(), m = A[0].length(), i, j;
            vector<bool> sorted(n - 1, false);
            for (j = 0; j < m; ++j) {
                for (i = 0; i < n - 1; ++i) {
                    if (!sorted[i] && A[i][j] > A[i + 1][j]) {
                        res++;
                        break;
                    }
                }
                if (i < n - 1) continue;
                for (i = 0; i < n - 1; ++i)
                    if (A[i][j] < A[i + 1][j])
                        sorted[i] = true;
            }
            return res;
        }
    

**Python, Using list,`O(NM)`, 64ms**

    
    
        def minDeletionSize(self, A):
            res, n, m =  0, len(A), len(A[0])
            is_sorted = [0] * (n - 1)
            for j in range(m):
                is_sorted2 = is_sorted[:]
                for i in range(n - 1):
                    if A[i][j] > A[i + 1][j] and is_sorted[i] == 0:
                        res += 1
                        break
                    is_sorted2[i] |= A[i][j] < A[i + 1][j]
                else:
                    is_sorted = is_sorted2
            return res
    

  

## More

Other version of python solution.

**Python, Brute force,`O(NlogN M^2)`， 230ms**

    
    
        def minDeletionSize(self, A):
             res = 0
            cur = [""] * len(A)
            for col in zip(*A):
                cur2 = zip(cur, col)
                if cur2 == sorted(cur2): cur = cur2
                else: res += 1
            return res
    

**Python, Using set,`O(NM)`, 24ms**

    
    
        def minDeletionSize(self, A):
            res, n, m =  0, len(A), len(A[0])
            unsorted = set(range(n - 1))
            for j in range(m):
                if any(A[i][j] > A[i + 1][j] for i in unsorted):
                    res += 1
                else:
                    unsorted -= {i for i in unsorted if A[i][j] < A[i + 1][j]}
            return res
    


### Solution 2
We use an array to record whether we need to compare

    
    
    from copy import copy
    
    class Solution(object):
        def minDeletionSize(self, A):
            """
            :type A: List[str]
            :rtype: int
            """
            A_len, str_len, res = len(A), len(A[0]), 0
            is_need_compare = [True] * A_len
    
            for i in xrange(str_len):
                tmp = copy(is_need_compare)
                for j in xrange(1, A_len):
                    if is_need_compare[j]:
                        if A[j][i] < A[j - 1][i]:
                            res += 1
                            is_need_compare = tmp
                            break
    
                        elif A[j][i] > A[j - 1][i]:
                            is_need_compare[j] = False
                                
            return res
    


### Solution 3
We analyze words one-by-one. If in a word `i` a character `j` breaks the
lexicographic order (`A[i - 1][j] > A[i][j]`), we add `j` column to the list
of deleted columns (I am using a hash set for O(1) operations).

After we deleted a column, we need to restart the process from the beginning.
The reason is that we a deleted column can break the lexicographic order for
words we already analyzed. Note that now we ignore all columns that are
already in the "deleted" list.

    
    
    int minDeletionSize(vector<string>& A) {
        unordered_set<int> di;
        for (auto i = 1; i < A.size(); ++i) {
            for (auto j = 0; j < A[i].size(); ++j) {
                if (di.count(j) > 0 || A[i - 1][j] == A[i][j]) continue;
                if (A[i - 1][j] > A[i][j]) {
                    di.insert(j);
                    i = 0;
                }
                break;
            }
        }
        return di.size();
    }
    



