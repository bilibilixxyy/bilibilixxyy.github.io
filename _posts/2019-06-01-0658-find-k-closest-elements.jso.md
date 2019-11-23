---
layout: post
title: 658. Find K Closest Elements
---
### Question
Given a sorted array, two integers `k` and `x`, find the `k` closest elements
to `x` in the array. The result should also be sorted in ascending order.If
there is a tie, the smaller elements are always preferred.

 **Example 1:**  

    
    
     **Input:** [1,2,3,4,5], k=4, x=3
    **Output:** [1,2,3,4]
    

**Example 2:**  

    
    
    **Input:** [1,2,3,4,5], k=4, x=-1
    **Output:** [1,2,3,4]
    

**Note:**  

  1. The value k is positive and will always be smaller than the length of the sorted array.
  2. Length of the given array is positive and will not exceed 104
  3. Absolute value of elements in the array and x will not exceed 104

* * *

 **UPDATE (2017/9/19):**  
The _arr_ parameter had been changed to an **array of integers** (instead of a
list of integers). **_Please reload the code definition to get the latest
changes_**.

### Solution 1
I binary-search for where the resulting elements start in the array. It's the
first index `i` so that `arr[i]` is better than `arr[i+k]` (with "better"
meaning closer to or equally close to `x`). Then I just return the `k`
elements starting there.

    
    
    def find_closest_elements(arr, k, x)
      arr[(0..arr.size).bsearch { |i| x - arr[i] <= (arr[i+k] || 1/0.0) - x }, k]
    end
    

I think that's simpler than binary-searching for `x` and then expanding to the
left and to the right like I've seen in other binary search solutions.

* * *

Here's a Java version without using the library's binary search:

    
    
    public List<Integer> findClosestElements(List<Integer> arr, int k, int x) {
        int lo = 0, hi = arr.size() - k;
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (x - arr.get(mid) > arr.get(mid+k) - x)
                lo = mid + 1;
            else
                hi = mid;
        }
        return arr.subList(lo, lo + k);
    }
    

The binary search costs O(log n) (actually also just O(log (n-k)) and the
`subList` call probably only takes O(1). As @sagimann [pointed
out](https://discuss.leetcode.com/post/211008), `subList` [returns a
view](https://docs.oracle.com/javase/8/docs/api/java/util/List.html#subList-
int-int-), not a separate copy. So it should only take O(1). Can be seen for
example in `ArrayList`'s
[subList](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/ArrayList.java#l980)
and the [`SubList`
constructor](http://hg.openjdk.java.net/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/ArrayList.java#l1001)
it calls. I also checked `LinkedList`, it gets its `subList` method inherited
from `AbstractList`, where it also takes only O(1). And `AbstractList` is a
basis for most lists.

 **Edit:** I also [implemented it in
Go](https://discuss.leetcode.com/topic/99831/o-log-n) now, to make it O(log
n). **Edit 2:** Ha, didn't have to do that, as the Java version apparently
already was O(log n) (I didn't originally know Java returns a view, only added
that now). **Edit 3:** Lol, I had mistakenly written "Python" in the title
instead of "Ruby" but apparently nobody noticed (and it's at 1800 views).
Fixed that now.


### Solution 2
##  **Intuition**

The array is sorted.  
If we want find the one number closest to `x`,  
we don't have to check one by one.  
it's straightforward to use binary research.

Now we want the `k` closest,  
the logic should be similar.  
  

##  **Explanation** :

Assume we are taking `A[i] ~ A[i + k -1]`.  
We can binary research `i`  
We compare the distance between `x - A[mid]` and `A[mid - k] - x`

If `x - A[mid] > A[mid + k] - x`,  
it means `A[mid + 1] ~ A[mid + k]` is better than `A[mid] ~ A[mid + k - 1]`,  
and we have `mid` smaller than the right `i`.  
So assign `left = mid + 1`.

Note that, you shouldn't compare the absolute value `abs(x - A[mid])` and
`abs(A[mid + k] - x)`.  
It's wrong though it get accepetd.  
It fails at the case A = [1,1,2,2,2,2,2,3,3], x=3, k=2

  

##  **Time Complexity** :

`O(log(N - K))` to binary research and find reseult  
`O(K)` to create the returned list.

  

 **Java:**

    
    
         public List<Integer> findClosestElements(int[] A, int k, int x) {
            int left = 0, right = A.length - k;
            while (left < right) {
                int mid = (left + right) / 2;
                if (x - A[mid] > A[mid + k] - x)
                    left = mid + 1;
                else
                    right = mid;
            }
            return Arrays.stream(A, left, left + k).boxed().collect(Collectors.toList());
        }
    

  

**C++:**

    
    
        vector< int> findClosestElements(vector<int>& A, int k, int x) {
            int left = 0, right = A.size() - k;
            while (left < right) {
                int mid = (left + right) / 2;
                if (x - A[mid] > A[mid + k] - x)
                    left = mid + 1;
                else
                    right = mid;
            }
            return vector<int>(A.begin() + left, A.begin() + left + k);
        }
    

  

**Python:**

    
    
        def findClosestElements(self, A, k, x):
             left, right = 0, len(A) - k
            while left < right:
                mid = (left + right) / 2
                if x - A[mid] > A[mid + k] - x:
                    left = mid + 1
                else:
                    right = mid
            return A[left:left + k]
    


### Solution 3
The idea is to find the first number which is equal to or greater than `x` in
`arr`. Then, we determine the indices of the start and the end of a subarray
in `arr`, where the subarray is our result. The time complexity is `O(logn +
k)`.

In the following code, `arr[index]` is the first number which is euqal to or
geater than `x` (if all numbers are less than `x`, `index` is `arr.size()`),
and the result is `arr[i+1, i+2, ... j]`.

Java version:

    
    
        public List<Integer> findClosestElements(List<Integer> arr, int k, int x) {
            int index = Collections.binarySearch(arr, x);
            if(index < 0) index = -(index + 1);
            int i = index - 1, j = index;                                    
            while(k-- > 0){
                if(i<0 || (j<arr.size() && Math.abs(arr.get(i) - x) > Math.abs(arr.get(j) - x) ))j++;
                else i--;
            }
            return arr.subList(i+1, j);
        }
    

C++ version:

    
    
        vector<int> findClosestElements(vector<int>& arr, int k, int x) {
            int index = std::lower_bound(arr.begin(), arr.end(), x) - arr.begin();
            int i = index - 1, j = index;                                    
            while(k--) (i<0 || (j<arr.size() && abs(arr[i] - x) > abs(arr[j] - x) ))? j++: i--;
            return vector<int>(arr.begin() + i + 1, arr.begin() + j );
        }
    



