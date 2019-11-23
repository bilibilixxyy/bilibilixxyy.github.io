---
layout: post
title: 1195. Distribute Candies to People
---
### Question
We distribute some number of `candies`, to a row of **`n = num_people`**
people in the following way:

We then give 1 candy to the first person, 2 candies to the second person, and
so on until we give `n` candies to the last person.

Then, we go back to the start of the row, giving `n + 1` candies to the first
person, `n + 2` candies to the second person, and so on until we give `2 * n`
candies to the last person.

This process repeats (with us giving one more candy each time, and moving to
the start of the row after we reach the end) until we run out of candies.  The
last person will receive all of our remaining candies (not necessarily one
more than the previous gift).

Return an array (of length `num_people` and sum `candies`) that represents the
final distribution of candies.



 **Example 1:**

    
    
     **Input:** candies = 7, num_people = 4
    **Output:** [1,2,3,1]
    **Explanation:**
    On the first turn, ans[0] += 1, and the array is [1,0,0,0].
    On the second turn, ans[1] += 2, and the array is [1,2,0,0].
    On the third turn, ans[2] += 3, and the array is [1,2,3,0].
    On the fourth turn, ans[3] += 1 (because there is only one candy left), and the final array is [1,2,3,1].
    

**Example 2:**

    
    
    **Input:** candies = 10, num_people = 3
    **Output:** [5,2,3]
    **Explanation:**
    On the first turn, ans[0] += 1, and the array is [1,0,0].
    On the second turn, ans[1] += 2, and the array is [1,2,0].
    On the third turn, ans[2] += 3, and the array is [1,2,3].
    On the fourth turn, ans[0] += 4, and the final array is [5,2,3].
    



 **Constraints:**

  * 1 <= candies <= 10^9
  * 1 <= num_people <= 1000

### Solution 1
  1. Use `give % num_people` to determine the current index of the people, where `give` is the `give-th` giving of candy;
  2. Increase each giving amount by 1 till run out of candies.

 **Java:**

    
    
         public int[] distributeCandies(int candies, int num_people) {
            int[] people = new int[num_people];
            for (int give = 0; candies > 0; candies -= give) {
                people[give % num_people] +=  Math.min(candies, ++give);
            }
            return people;
        }
    

* * *

**Python3:**

    
    
        def distributeCandies(self, candies:  int, num_people: int) -> List[int]:
            people = num_people * [0]
            give = 0
            while candies > 0:
                people[give % num_people] += min(candies, give + 1)
                give += 1
                candies -= give
            return people
    

**Analysis:**  
Assume there are `give` times distribution such that `1 + 2 + ... + give >=
candies`. Therefore,  
`(1 + give) * give / 2 >= candies`, and when `give` is big enough, `(give + 1)
* give /2 ~ candies`. We have:

    
    
     1/2 * give ^ 2 < 1/2 * (give ^ 2 + give)  < 1/ 2 * (give + 1) ^ 2
    

then

    
    
    1/2 * give ^ 2 < candies < 1/ 2 * (give + 1) ^ 2
    

Time: O(sqrt(candies)), space: O(num_people).


### Solution 2
I was going down the mathematical path and it felt a bit tricky. Since the
problem is 'Easy', I guessed the brute-force should do for the contest.

    
    
    vector<int> distributeCandies(int c, int num) {
      vector<int> res(num);
      for (auto i = 0; c > 0; c -= ++i) 
        res[i % num] += min(i + 1, c);
      return res;
    }
    


### Solution 3
## **Math**

Math solution is `O(N)`, but personally not really like it.

    
    
        def distributeCandies(self, candies, n):
            x = int(math.sqrt(candies * 2 + 0.25) - 0.5)
            res = [0] * n
            for i in xrange(n):
                m = x / n + (x % n > i)
                res[i] = m * (i + 1) + m * (m - 1) / 2 * n
            res[x % n] += candies - x * (x + 1) / 2
            return res
    

## **Intuition**

Brute force of simulation seems to be easy.  
But how is the time complexity?  
  

## **Explanation**

The `i-th` distribution,  
we will distribute `i + 1` candies to `(i % n)`th people.  
We just simulate the process of distribution until we ran out of candies.

## **Complexity**

Time `O(sqrt(candies))`  
Space `O(N)` for result

The number of given candies is `i + 1`, which is an increasing sequence.  
The total number distributed candies is `c * (c + 1) / 2` until it's bigger
than `candies`.  
So the time it takes is `O(sqrt(candies))`  
  

**Java:**

    
    
         public int[] distributeCandies(int candies, int n) {
            int[] res = new int[n];
            for (int i = 0; candies > 0; ++i) {
                res[i % n] += Math.min(candies, i + 1);
                candies -= i + 1;
            }
            return res;
        }
    

**C++:**

    
    
         vector<int> distributeCandies(int candies, int n) {
            vector<int> res(n);
            for (int i = 0; candies > 0; ++i) {
                res[i % n] += min(candies, i + 1);
                candies -= i + 1;
            }
            return res;
        }
    

**Python**

    
    
         def distributeCandies(self, candies, n):
            res = [0] * n
            i = 0
            while candies > 0:
                res[i % n] += min(candies, i + 1)
                candies -= i + 1
                i += 1
            return res
    



