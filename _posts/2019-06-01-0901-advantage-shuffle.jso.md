---
layout: post
title: 901. Advantage Shuffle
---
### Question
Given two arrays `A` and `B` of equal size, the _advantage of`A` with respect
to `B`_ is the number of indices `i` for which `A[i] > B[i]`.

Return **any** permutation of `A` that maximizes its advantage with respect to
`B`.



 **Example 1:**

    
    
     **Input:** A = [2,7,11,15], B = [1,10,4,11]
    **Output:** [2,11,7,15]
    

**Example 2:**

    
    
    **Input:** A = [12,24,8,32], B = [13,25,32,11]
    **Output:** [24,32,8,12]
    



 **Note:**

  1. `1 <= A.length = B.length <= 10000`
  2. `0 <= A[i] <= 10^9`
  3. `0 <= B[i] <= 10^9`

### Solution 1
Contest version below:

    
    
    class Solution {
        public int[] advantageCount(int[] A, int[] B) {
            Arrays.sort(A);
            int n=A.length;
            int[] res= new int[n];
            PriorityQueue<int[]> pq= new PriorityQueue<>((a,b)->b[0]-a[0]);
            for (int i=0; i<n; i++) pq.add(new int[]{B[i], i});
            int lo=0, hi=n-1;
            while(!pq.isEmpty()){
                int[] cur= pq.poll();
                int idx=cur[1], val=cur[0];
                if (A[hi]>val) res[idx]=A[hi--];
                else res[idx]=A[lo++];
            }
            return res;
        }  
    }
    

![image](https://s3-lc-
upload.s3.amazonaws.com/users/caraxin/image_1531625378.png)  
Shorter version just for fun (promise me never use it during an interview):

    
    
    class Solution {
        public int[] advantageCount(int[] A, int[] B) {
            Arrays.sort(A);
            PriorityQueue<int[]> pq= new PriorityQueue<>((a,b)->b[0]-a[0]);
            for (int i=0; i<B.length; i++) pq.add(new int[]{B[i], i});
            int lo=0, hi=A.length-1, res[] = new int[A.length];
            while(!pq.isEmpty()) res[pq.peek()[1]]=pq.poll()[0]<A[hi]?A[hi--]:A[lo++];
            return res;
        }
    }
    

If you are chasing speed, it's time to forget lambda, beats 90%:

    
    
    class Solution {
        public int[] advantageCount(int[] A, int[] B) {
            Arrays.sort(A);
            int n=A.length;
            int[] res= new int[n];
            PriorityQueue<int[]> pq= new PriorityQueue<>(new Comparator<int[]>(){
                public int compare(int[] a, int[] b){
                    return b[0]-a[0];
                }
            });
            for (int i=0; i<n; i++) pq.add(new int[]{B[i], i});
            int lo=0, hi=n-1;
            while(!pq.isEmpty()){
                int[] cur= pq.poll();
                int idx=cur[1], val=cur[0];
                if (A[hi]>val) res[idx]=A[hi--];
                else res[idx]=A[lo++];
            }
            return res;
        }  
    }
    

Happy coding.


### Solution 2
For each B[i], we select the smallest number in A that is greater than B[i].
If there are no such number, we select the smalest number in A.

I am usign multiset to sort and keep track of numbers in A. After a number is
selected, we need to remove it from the multiset (erase by iterator takes a
constant time).

    
    
    vector<int> advantageCount(vector<int>& A, vector<int>& B) {
      multiset<int> s(begin(A), end(A));
      for (auto i = 0; i < B.size(); ++i) {
        auto p = *s.rbegin() <= B[i] ? s.begin() : s.upper_bound(B[i]);
        A[i] = *p;
        s.erase(p);
      }
      return A;
    }
    


### Solution 3
  1. Sort `A`
  2. For every element `b` in B from big to small,  
if `A[-1] > b`, then this `b` will take `A.pop()`

  3. Assign all elements in B an element from `take` or the rest of `A`.

 **Time Complexity** :  
O(NlogN)

    
    
        def advantageCount(self, A, B):
            A = sorted(A)
            take = collections.defaultdict(list)
            for b in sorted(B)[::-1]:
                if b < A[-1]: take[b].append(A.pop())
            return [(take[b] or A).pop() for b in B]
    



