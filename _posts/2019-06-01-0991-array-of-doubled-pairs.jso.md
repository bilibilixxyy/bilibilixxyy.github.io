---
layout: post
title: 991. Array of Doubled Pairs
---
### Question
Given an array of integers `A` with even length, return `true` if and only if
it is possible to reorder it such that `A[2 * i + 1] = 2 * A[2 * i]` for every
`0 <= i < len(A) / 2`.



 **Example 1:**

    
    
     **Input:** [3,1,3,6]
    **Output:** false
    

**Example 2:**

    
    
    **Input:** [2,1,2,6]
    **Output:** false
    

**Example 3:**

    
    
    **Input:** [4,-2,2,-4]
    **Output:** true
    **Explanation:** We can take two groups, [-2,-4] and [2,4] to form [-2,-4,2,4] or [2,4,-2,-4].
    

**Example 4:**

    
    
    **Input:** [1,2,4,16,8,4]
    **Output:** false
    



 **Note:**

  1. `0 <= A.length <= 30000`
  2. `A.length` is even
  3. `-100000 <= A[i] <= 100000`

### Solution 1
##  **Intuition**

 **Let's see a simple case**  
Assume all interger are positive, for example `[2,4,4,8]`.  
We have one `x = 2`, we need to match it with one `2x = 4`.  
Then one `4` is gone, we have the other `x = 4`.  
We need to match it with one `2x = 8`.  
Finaly no number left.

 **Why we start from`2`?**  
Because it's the smallest and we no there is no `x/2` left.  
So we know we need to find `2x`

 **What if the case negative?**  
One way is that start from the biggest (with abosolute value smallest),  
and we aplly same logic.

Another way is that start from the smallest (with abosolute value biggest),  
and we try to find `x/2` each turn.  
  

##  **Explanation**

  1. Count all numbers.
  2. Loop all numbers on the order of its absolute.  
We have `counter[x]` of `x`, so we need the same amount of `2x` wo match them.  
If `c[x] > c[2 * x]`, then we return false  
If `c[x] <= c[2 * x]`, then we do `c[2 * x] -= c[x]` to remove matched `2x`.

Don't worry about `0`, it doesn't fit the logic above but it won't break our
algorithme.

In case count[0] is odd, it won't get matched in the end.  
(Anyway you can return false earlier here)

In case count[0] is even, we still have `c[0] <= c[2 * 0]`.  
And we still need to check all other numbers.  
  

 **Java, O(NlogK):**

    
    
        public  boolean canReorderDoubled(int[] A) {
            Map<Integer, Integer> count = new TreeMap<>();
            for (int a : A)
                count.put(a, count.getOrDefault(a, 0) + 1);
            for (int x : count.keySet()) {
                if (count.get(x) == 0) continue;
                int want = x < 0 ? x / 2 : x * 2;
                if (x < 0 && x % 2 != 0 || count.get(x) > count.getOrDefault(want, 0))
                    return false;
                count.put(want, count.get(want) - count.get(x));
            }
            return true;
        }
    

**C++, O(N + KlogK)**

    
    
         bool canReorderDoubled(vector<int>& A) {
            unordered_map<int, int> c;
            for (int a : A) c[a]++;
            vector<int> keys;
            for (auto it : c)
                keys.push_back(it.first);
            sort(keys.begin(), keys.end(), [](int i, int j) {return abs(i) < abs(j);});
            for (int x : keys) {
                if (c[x] > c[2 * x])
                    return false;
                c[2 * x] -= c[x];
            }
            return true;
        }
    

**Python, O(N + KlogK), 100~200ms**

    
    
        def canReorderDoubled( self, A):
            c = collections.Counter(A)
            for x in sorted(c, key=abs):
                if c[x] > c[2 * x]:
                    return False
                c[2 * x] -= c[x]
            return True
    


### Solution 2
  1. We sort the list (ascending) by absolute value and make sure the negative ones come before the positive ones.
  2. Then we use two pointers. Since the values are ascending (by abs), the fast pointer never looks back and will only reach the end of the list if it falis to find a 2*x and returns false. When it does find a match, it marks the item with a dummy value so that the slow pointer can skip it.
  3. Time is O(NlogN), Space is O(1).

    
    
    class Solution:
        def canReorderDoubled(self, A):
            """
            :type A: List[int]
            :rtype: bool
            """
            dummy = 1000000
            A.sort(key=lambda x:(x>0,abs(x)))
            size =len(A)
            fast=0
            for i in range(size):
                if A[i]==dummy: continue
                if fast<=i: fast = i+1
                while fast<size and A[fast]!=2*A[i]: fast+=1
                if fast==size: return False
                A[fast] = dummy
            return True
    


### Solution 3
// (0). one map only: to count the frequency of apperance of each number  
// (1). Sort the array, then scan from the smalllest to find (x, 2x) pairs  
// ___ (a) for negative number, we will see 2x first, the match should be half
of it.  
// ___ (b). for positive, we will see x first, the match should be two times
of it.  
// (2) once matched, update the map  
// (3). no need to put 0 into the map.  
Example: [3, 6, -4, -2, 0, 0, 14, 7, -2, -4]  
Your stdout  
map: {-2=2, -4=2, 3=1, 6=1, 7=1, 14=1}  
map: {-2=0, -4=0, 3=1, 6=1, 7=1, 14=1} after process (-4, -2) pair  
map: {-2=0, -4=0, 3=0, 6=0, 7=1, 14=1} after process (3, 6) pair  
map: {-2=0, -4=0, 3=0, 6=0, 7=0, 14=0} after process (7, 14) pair  
Your answer: true

    
    
    ```
    public boolean canReorderDoubled(int[] A) {
        
        int n = A.length;
        if (n % 2 != 0) return false;
        Arrays.sort(A);
        Map<Integer, Integer> map = new HashMap<>(); // <num, freq>
        for (int item : A) {
            if (item == 0) continue;
            map.put(item, map.getOrDefault(item, 0) + 1);
        }
        for (int i = 0; i < n; i++) {
            if (A[i] != 0 && map.get(A[i]) > 0) {
                //System.out.println("map: " + map);
                int target = A[i] * 2; // (3). positive, target = 2x 
                if (A[i] < 0) { // (2) for negative (-4), the match should be half of it. 
                    if (A[i] % 2 != 0) { // A[i] = -7, A[i] / 2 = -3
                        return false;
                    } else {
                        target = A[i] / 2; // negative: target = x
                    }
                }
                if (map.getOrDefault(target, 0) < map.get(A[i])) {
                    return false;
                } else {
                    // (4) once matched, update the map
                    map.put(target, map.get(target)  - map.get(A[i]));
                    map.put(A[i], 0);
                }
            }
        }
        //System.out.println("map: " + map);
        return true;
    
        
    }
    



