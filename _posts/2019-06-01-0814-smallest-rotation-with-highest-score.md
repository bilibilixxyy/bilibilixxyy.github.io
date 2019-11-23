---
layout: post
title: 814. Smallest Rotation with Highest Score
---
### Question
 Given an array `A`, we may rotate it by a non-negative integer `K` so that
the array becomes `A[K], A[K+1], A{K+2], ... A[A.length - 1], A[0], A[1], ...,
A[K-1]`.  Afterward, any entries that are less than or equal to their index
are worth 1 point.

For example, if we have `[2, 4, 1, 3, 0]`, and we rotate by `K = 2`, it
becomes `[1, 3, 0, 2, 4]`.  This is worth 3 points because 1 > 0 [no points],
3 > 1 [no points], 0 <= 2 [one point], 2 <= 3 [one point], 4 <= 4 [one point].

Over all possible rotations, return the rotation index K that corresponds to
the highest score we could receive.  If there are multiple answers, return the
smallest such index K.

    
    
     **Example 1:**
    **Input:** [2, 3, 1, 4, 0]
    **Output:** 3
    **Explanation:** 
    Scores for each K are listed below: 
    K = 0,  A = [2,3,1,4,0],    score 2
    K = 1,  A = [3,1,4,0,2],    score 3
    K = 2,  A = [1,4,0,2,3],    score 3
    K = 3,  A = [4,0,2,3,1],    score 4
    K = 4,  A = [0,2,3,1,4],    score 3
    

So we should choose K = 3, which has the highest score.



    
    
    **Example 2:**
    **Input:** [1, 3, 0, 2, 4]
    **Output:** 0
    **Explanation:** A will always have 3 points no matter how it shifts.
    So we will choose the smallest K, which is 0.
    

**Note:**

  * `A` will have length at most `20000`.
  * `A[i]` will be in the range `[0, A.length]`.

### Solution 1
 **Key point**  
Don't calculate the score for `K=0`, we don't need it at all.  
(I see almost all other solutions did)  
The key point is to find out how score changes when `K++`

 **Time complexity:**  
“A will have length at most 20000.”  
I think it means you should find a O(N) solution.

 **How dosen score change when K++ ?**

  *  **Get point**  
Each time when we rotate, we make index `0` to index `N-1`, then we get one
more point.  
We know that for sure, so I don't need to record it.

  *  **Lose point**  
`(i - A[i] + N) % N` is the value of K making `A[i]'s` index just equal to
`A[i]`.  
For example, If `A[6] = 1`, then `K = (6 - A[6]) % 6 = 5` making `A[6]` to
`index 1` of new array.  
So when `K=5`, we get this point for `A[6]`  
Then if `K` is bigger when `K = (i - A[i] + 1) % N`, we start to lose this
point, making our score -= 1  
All I have done is record the value of `K` for all `A[i]` where we will lose
points.

  *  **`A[i]=0`**  
Rotation makes no change for it, becasue we alwars have `0 <= index`.  
However, it is covered in "get point" and "lose point".

 **Explanation of codes**

  1. Search the index where score decrease and record this changement to a list `change`.
  2. A simple for loop to calculate the score for every `K` value.  
`score[K] = score[K-1] + change[K]`  
In my codes I accumulated `changes` so I get the changed score for every `K`
value compared to `K=0`

  3. Find the index of best score.

C++:

    
    
        int bestRotation(vector<int>& A) {
            int N = A.size();
            int change[N] = {0};
            for (int i = 0; i < N; ++i) change[(i - A[i] + 1 + N) % N] -= 1;
            for (int i = 1; i < N; ++i) change[i] += change[i - 1] + 1;
            return distance(change, max_element(change, change + N));
        }
    

Java

    
    
        public int bestRotation(int[] A) {
            int N = A.length;
            int change[] = new int[N];
            for (int i = 0; i < N; ++i) change[(i - A[i] + 1 + N) % N] -= 1;
            int max_i = 0;
            for (int i = 1; i < N; ++i) {
                change[i] += change[i - 1] + 1;
                max_i = change[i] > change[max_i] ? i : max_i;
            }
            return max_i;
        }
    

Python

    
    
        def bestRotation(self, A):
            N = len(A)
            change = [1] * N
            for i in range(N): change[(i - A[i] + 1) % N] -= 1
            for i in range(1, N): change[i] += change[i - 1]
            return change.index(max(change))
    


### Solution 2
The trick of this problem is to convert it into finding most overlap interval
problem (like meeting room II, finding most airplanes at a time)

    
    
    class Solution {
        public int bestRotation(int[] A) {
            int n = A.length;
            int[] a = new int[n];  // to record interval start/end
            for (int i = 0; i < A.length; i++) {
                a[(i + 1) % n]++;             // interval start
                a[(i + 1 - A[i] + n) % n]--;  // interval end
            }
            int count = 0;
            int maxCount = -1;
            int res = 0;
            for (int i = 0; i < n; i++) {  // find most overlap interval
                count += a[i];
                if (count > maxCount) {
                    res = i;
                    maxCount = count;
                }
            }
            return res;
        }
    }
    


### Solution 3
Let's go through an example first. If the input is{2,4,1,3,0}, We write down
all status when rotate k steps.  
Number:::::: 2 4 1 3 0  
k = 0 index: 0 1 2 3 4  
k = 1 index: 4 0 1 2 3  
k = 2 index: 3 4 0 1 2  
k = 3 index: 2 3 4 0 1  
k = 4 index: 1 2 3 4 0

If we look in each column, then index for one particular number is somehow
decrease with a valley(0). We want to know in which K steps the index is
greate or equal than the corresponding number. For number 2, k should
be{1,2,3}, for number 1, k should be{0,1,3,4}.  
So our task is to collect all k rows in which the index >= number for each
paritular number.

  1. If number > index in original array:  
As index is decreasing, so we need to find in which row the index is equals to
(length - 1), the largest index of array. Then go down until index == number.

  2. If number <= index in original array:  
k = 0 is always valid, and 0 through the row in which index == number is the
last valid row. Also, don't forget the index == len - 1 this corner case;

There are some math tricks from maybe high school: largest index row = (index
+ 1) % len; Row in which index == number = (index + len - num) % len;  
We just need to update count[min] and count[max] to avoid O(n) time to record
our result.

    
    
    class Solution {
        public int bestRotation(int[] A) {
            int[] count = new int[A.length + 1];
            int len = A.length;
            for(int index = 0; index < A.length; index ++){
                int num = A[index];
                if(index < num){
                    int maxLenK = (index + 1) % len;
                    int ori = (index + len - num) % len;
                    count[maxLenK] ++;
                    count[ori + 1] --;
                }else{      // index >= num
                    int ori = (index + len - num) % len;
                    count[0] ++;
                    count[ori + 1] --;
                    if(index != len - 1){
                        count[(index + 1) % len] ++;
                        count[len] --;
                    }
                }
            }
            int max = count[0];
            int res = 0;
            for(int i = 1; i < len; i ++){
                count[i] = count[i] + count[i - 1];
                if(count[i] > max){
                    max = count[i];
                    res = i;
                }            
            }
            return res;
        }
    }
    



