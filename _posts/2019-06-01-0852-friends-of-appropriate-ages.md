---
layout: post
title: 852. Friends Of Appropriate Ages
---
### Question
Some people will make friend requests. The list of their ages is given and
`ages[i]` is the age of the ith person.

Person A will NOT friend request person B (B != A) if any of the following
conditions are true:

  * `age[B] <= 0.5 * age[A] + 7`
  * `age[B] > age[A]`
  * `age[B] > 100 && age[A] < 100`

Otherwise, A will friend request B.

Note that if A requests B, B does not necessarily request A.  Also, people
will not friend request themselves.

How many total friend requests are made?

 **Example 1:**

    
    
     **Input:** [16,16]
    **Output:** 2
    **Explanation:** 2 people friend request each other.
    

**Example 2:**

    
    
    **Input:** [16,17,18]
    **Output:** 2
    **Explanation:** Friend requests are made 17 -> 16, 18 -> 17.

**Example 3:**

    
    
    **Input:** [20,30,100,110,120]
    **Output:**
    **Explanation:** Friend requests are made 110 -> 100, 120 -> 110, 120 -> 100.
    



Notes:

  * `1 <= ages.length <= 20000`.
  * `1 <= ages[i] <= 120`.

### Solution 1
age[B] > age[A] and age[B] > 100 && age[A] < 100, isn't the latter redundant?


### Solution 2
Three conditions could be merged to one:  
The Person with age A can request person with age B if

  * B is in range **(** 0.5 * A + 7, A **]**

    
    
         public int numFriendRequests(int[] ages) {
            int res = 0;
            int[] numInAge = new int[121], sumInAge = new int[121];
            
            for(int i : ages) 
                numInAge[i] ++;
            
            for(int i = 1; i <= 120; ++i) 
                sumInAge[i] = numInAge[i] + sumInAge[i - 1];
            
            for(int i = 15; i <= 120; ++i) {
                if(numInAge[i] == 0) continue;
                int count = sumInAge[i] - sumInAge[i / 2 + 7];
                res += count * numInAge[i] - numInAge[i]; //people will not friend request themselves, so  - numInAge[i]
            }
            return res;
        }
    


### Solution 3
Since the age range is limited to [1..120], we can count number of people of
each age and store in the array. Then we can use that array to count friend
requests:

  * People younger than 15 cannot make requests due to the first rule.
  * From the age of 15, people can make requests to the same age: `a[i] * (a[i] - 1)` requests.
  * People can make requests to younger people older than 0.5 * i + 7: `a[j] * a[i]` requests.
    * The third rule is redundant as the condition is already covered by the second rule.

The complexity is O(n) as we need to count ages in the input array. Counting
requests is O(1) as it always takes 2862 operations precisely.

    
    
    int numFriendRequests(vector<int>& ages) {
      int a[121] = {}, res = 0;
      for (auto age : ages) ++a[age];
      for (auto i = 15; i <= 120; ++i)
        for (int j = 0.5 * i + 8; j <= i; ++j) res += a[j] * (a[i] - (i == j));
      return res;
    }
    

We can optimize the counting by using a sliding sum of friend requests. Note
that the sum starts at the minimum age (`0.5 * i + 7`), per the first rule.

The complexity of optimized solution is O(n + m) vs. O(n + m * m), where m is
the age range. In our case, we need only 159 operations for [1..120] range. I
did not see much of a difference in the OJ runtime, though, probably because
of the tight constraint. The second solution will shine if we need, for
example, use days instead of years to compare ages.

    
    
    int numFriendRequests(vector<int>& ages) {
      int a[121] = {}, res = 0;
      for (auto age : ages) ++a[age];
      for (auto i = 15, minAge = 15, sSum = 0; i <= 120; sSum += a[i], res += a[i++] * (sSum - 1))
        while (minAge <= 0.5 * i + 7) sSum -= a[minAge++];
      return res;
    }
    



