---
layout: post
title: 842. Card Flipping Game
---
### Question
On a table are `N` cards, with a positive integer printed on the front and
back of each card (possibly different).

We flip any number of cards, and after we choose one card.

If the number `X` on the back of the chosen card is not on the front of any
card, then this number X is good.

What is the smallest number that is good?  If no number is good, output `0`.

Here, `fronts[i]` and `backs[i]` represent the number on the front and back of
card `i`.

A flip swaps the front and back numbers, so the value on the front is now on
the back and vice versa.

 **Example:**

    
    
     **Input:** fronts = [1,2,4,4,7], backs = [1,3,4,1,3]
    **Output:** 2
    **Explanation:** If we flip the second card, the fronts are [1,3,4,4,7] and the backs are [1,2,4,1,3].
    We choose the second card, which has number 2 on the back, and it isn't on the front of any card, so 2 is good.



 **Note:**

  1. `1 <= fronts.length == backs.length <= 1000`.
  2. `1 <= fronts[i] <= 2000`.
  3. `1 <= backs[i] <= 2000`.

### Solution 1
I dont know if its just me - but the problem statement is so confusing.
Especially "is not on the front of any card,"

Even after looking at this forum and accepted solutions - the problem
statement is still not making sense.

+1 for down vote.


### Solution 2
If `fronts[i] == backs[i]`, it means that `fronts[i]` is sure to appear on the
table, no matter how we flap this card.  
In case that `fronts[i]` and `backs[i]` are same, `fronts[i]` become
impossible to be good number, so I add it to a set `same`.

If `fronts[i] != backs[i]`, we can always hide either number by flapping it to
back.

Then loop on all numbers and return the minimum.

C++

    
    
        int flipgame(vector<int>& f, vector<int>& b) {
            unordered_set<int> same;
            for (int i = 0; i < f.size(); ++i) if (f[i] == b[i]) same.insert(f[i]);
            int res = 3000;
            for (int & i : f) if (same.count(i) == 0) res = min(res, i);
            for (int & i : b) if (same.count(i) == 0) res = min(res, i);
            return res % 3000;
        }
    

Java

    
    
        public int flipgame(int[] f, int[] b) {
            HashSet<Integer> same = new HashSet<>();
            for (int i = 0; i < f.length; ++i) if (f[i] == b[i]) same.add(f[i]);
            int res = 3000;
            for (int i : f) if (!same.contains(i)) res = Math.min(res, i);
            for (int i : b) if (!same.contains(i)) res = Math.min(res, i);
            return res % 3000;
        }
    

Python

    
    
        def flipgame(self, f, b):
            same = {x for x, y in zip(f, b) if x == y}
            return min([i for i in f + b if i not in same] or [0])
    


### Solution 3
At the first glance I even don't know what is this game going to do. It takes
me a lot of time to figure out what does the description mean. Finally I
realize it is a simple problem using HashSet.

The intuition behind my solution is: If a card has the same number both on the
front side and the back side, then this number is fixed, which means this
number will never be a good number. On the other hand, for other numbers which
are not fixed, after several flips, we could always find a way to flip this
number to the front when currently this number is not showing.  
 **The problem's meaning is if you could find a way to flip it up when
currently this number is not showing on any of the card(including the chosen
one), then this number could be considered as a good number.**

We check whether the number is in the hashset because if a number is in the
hashset, this number will always face to us. Make sense?

Consider an example: `fronts = [1,2,2,4,2], backs = [1,3,4,1,3]`, in this
case, hashset will only contains a number `1`.  
After we build up our hashset, we check the fronts number and backs number one
by one. When we encounter `1`, it has been in the hashset, which means it will
never be a good number because there will always be a `1` faces to us.  
However, for other numbers, which are not presented in the hashset, we could
always find a way to filp it up as a good number. i.e. for number `2`, firstly
we flip the `second`, `third`, `fifth` card so that all the `2`s are in the
back side. Now the cards become `[1, 3, 4, 4, 3] and [1, 2, 2, 1, 2]` Then we
flip any of `second`, `third`, `fifth`. At this time, `2` will be considered
as a good number. Since the 2 is the minimum valid good number. It will be the
result.

    
    
    class Solution {
        public int flipgame(int[] fronts, int[] backs) {
            Set<Integer> set = new HashSet<>();
            int n = fronts.length;
            for (int i = 0; i < n; i++) {
                if (fronts[i] == backs[i]) {
                    set.add(fronts[i]);
                }
            }
            int min = Integer.MAX_VALUE;
            for (int i = 0; i < n; i++) {
                if (!set.contains(backs[i])) {
                    min = Math.min(min, backs[i]);
                }
                if (!set.contains(fronts[i])) {
                    min = Math.min(min, fronts[i]);
                }
            }
            return min == Integer.MAX_VALUE ? 0 : min;
        }
    }
    



