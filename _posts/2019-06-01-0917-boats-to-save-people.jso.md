---
layout: post
title: 917. Boats to Save People
---
### Question
The `i`-th person has weight `people[i]`, and each boat can carry a maximum
weight of `limit`.

Each boat carries at most 2 people at the same time, provided the sum of the
weight of those people is at most `limit`.

Return the minimum number of boats to carry every given person.  (It is
guaranteed each person can be carried by a boat.)



 **Example 1:**

    
    
     **Input:** people = [1,2], limit = 3
    **Output:** 1
    **Explanation:** 1 boat (1, 2)
    

**Example 2:**

    
    
    **Input:** people = [3,2,2,1], limit = 3
    **Output:** 3
    **Explanation** : 3 boats (1, 2), (2) and (3)
    

**Example 3:**

    
    
    **Input:** people = [3,5,3,4], limit = 5
    **Output:** 4
    **Explanation** : 4 boats (3), (3), (4), (5)

 **Note** :

  * `1 <= people.length <= 50000`
  * `1 <= people[i] <= limit <= 30000`

### Solution 1
Push from two ends of the sorted array _**people**_.

  1. if the 2 ends, `people[lo]` and `people[hi]`, can fit in a boat, move the 2 ends;
  2. otherwise move only the high end, and the low end stays.  
In other words: **always move the high end, but the low end depends on if it
can cram into a boat with the high end.**

  3. repeat the above till the 2 ends meet.

    
    
        public int numRescueBoats(int[] people, int limit) {
            Arrays.sort(people);
            int ans = 0; 
            for (int hi = people.length - 1, lo = 0; hi >= lo; --hi, ++ans) { // high end always moves
                if (people[lo] + people[hi] <= limit) { ++lo; } // low end moves only if it can fit in a boat with high end.
            }
            return ans;
        }
    

**Update:**  
Since some people challenge or have questions about the greedy algorithm, I
provide the proof as follows.

 **Proof of the correctness of the greedy algorithm:**  
Denote `S` as one of the optimal solutions, and `O` as our algorithm output
solution.  
 **1\. Greedy choice property.**

Starting from the **heaviest person hi** , there are 2 possible cases:  
a) if `hi` can NOT fit in a boat with any other, then both in `S` and `O`,
`hi` is in a boat **alone**. Apparently, our first step is optimal and the
greedy choice property holds;  
b) if `hi` CAN fit in a boat with at least 1 other person, then  
in `O`, `hi` and **lightest person lo** together must be in same boat,
according to our algorithm.

In `S`, if they are also in same boat, then our first step is optimal and the
greedy choice property holds;  
If `hi` and `lo` are not in same boat, say in `boat-hi` and `boat-lo`
respectively, then we can swap `hi` with `lo`'s boat mate, say `m`. Obviously,
`m <= hi`, therefore the swap is feasible. Since the swap results no extra
boat(s), a new optimal solution `T` is obtained. That indicates our first step
--put `hi` and `lo` into same boat--is an optimal step and and greedy choice
property also holds.

 **2.optimal substructure property.**

Let `P` be the original problem at scale `n`, where `n = people.length`. From
the above 1, after first step, we have a subproblem `P'` at scale `n'`(`n' = n
- 1 or n - 2`, depends on `hi` in a boat alone or not). Similary, we have
`hi'` and `lo'` and can do next step as in 1.

Since in 1 we proved `T` is an optimal solution, and the solution of `P'`, say
`O'`, contained within `T` is also an optimal one. Thus the the problem has
the optimal substructure property.

Combine 1 and 2, we complete the proof.

 **end of proof.**


### Solution 2
Sort people.  
For the current heaviest person, we try to let him go with the lightest
person.  
As all people need to get on the boat.  
If the sum of weight is over the limit, we have to let the heaviest go alone.  
No one can take the same boat with him.

At the end of for loop, it may happend that `i = j`.  
But it won't change the result so don't worry about it.

 **Time Complexity** :  
O(NlogN)

 **C++:**

    
    
        int numRescueBoats(vector<int> people, int limit) {
            int  i, j;
            sort(people.rbegin(), people.rend());
            for (i = 0, j = people.size() - 1; i <= j; ++i)
                if (people[i] + people[j] <= limit) j--;
            return i;
        }
    

**Java:**

    
    
        public int numRescueBoats(int[] people, int limit) {
            Arrays. sort(people);
            int i, j;
            for (i = 0, j = people.length - 1; i <= j; --j)
                if (people[i] + people[j] <= limit) ++i;
            return people.length - 1 - j;
        }
    

**Python:**

    
    
        def numRescueBoats(self, people, limit):
            people. sort(reverse=True)
            i, j = 0, len(people) - 1
            while i <= j:
                if people[i] + people[j] <= limit: j -= 1
                i += 1
            return i
    


### Solution 3
the O(nlogn) solution wasn't difficult to came up with after sorting the
array. We simply maintain 2 pointers `start` and `end`. If `people[start] +
people[end] <= limit` meaning both start and end can put in the same boat,
hence we increament `start` and decrement `end`. If `people[start] +
people[end] > limit` meaning only `end` can be on the boad, hence we decrement
`end` and keep `start`.

    
    
    public int numRescueBoats(int[] people, int limit) {
            Arrays.sort(people);
            int solution = 0;
            int start = 0;
            int end = people.length -1;
            
            while(start <= end) {
                solution++;
                if(start == end) break; // last person on boat
                if(people[start] + people[end] <= limit) start++; // we can carry two people
                end--;
            }
            return solution;
        }
    

Time: O(nlogn)  
Space: O(1)

We can see the main cost for this logic is the `Arrays.sort()`. After seeing
the hint "It is guaranteed each person can be carried by a boat", we know that
the number in the array will not be larger than `limit`; Hence, it is suitable
for bucket sort. Once we sort the array, the remain logic is the same. The
only difference is that we need make sure `start` and `end` are pointing to a
valid person, since the bucket might be 0;

    
    
    public int numRescueBoats(int[] people, int limit) {
            int[] buckets = new int[limit+1];
            for(int p: people) buckets[p]++;
            int start = 0;
            int end = buckets.length - 1;
            int solution = 0;
            while(start <= end) {
    			//make sure the start always point to a valid number
                while(start <= end && buckets[start] <= 0) start++;
    			//make sure end always point to valid number
                while(start <= end && buckets[end] <= 0) end--;
    			//no one else left on the ship, hence break.
                if(buckets[start] <= 0 && buckets[end] <= 0) break;
                solution++;
                if(start + end <= limit) buckets[start]--; // both start and end can carry on the boat
                buckets[end]--;
            }
            return solution;
        }
    

Of course engineering is all about trade offs.  
Time: O(n)  
Space: O(limit)



