---
layout: post
title: 374. Guess Number Higher or Lower
---
### Question
We are playing the Guess Game. The game is as follows:

I pick a number from **1** to **_n_**. You have to guess which number I
picked.

Every time you guess wrong, I'll tell you whether the number is higher or
lower.

You call a pre-defined API `guess(int num)` which returns 3 possible results
(`-1`, `1`, or `0`):

    
    
    -1 : My number is lower
     1 : My number is higher
     0 : Congrats! You got it!
    

**Example :**

    
    
    **Input:** n = 10, pick = 6
    **Output:** 6
    

### Solution 1
-1 : My number is lower  
1 : My number is higher  
0 : Congrats! You got it!

Here "My" means the number which is given for you to guess not the number you
put into **guess(int num).**


### Solution 2
Using binary search to find the smallest number that's not too small.

    
    
        def guessNumber(self, n):
            class C: __getitem__ = lambda _, i: -guess(i)
            return bisect.bisect(C(), -1, 1, n)
    

Alternatively, without using the library:

    
    
        def guessNumber(self, n):
            lo, hi = 1, n
            while lo < hi:
                mid = (lo + hi) / 2
                if guess(mid) == 1:
                    lo = mid + 1
                else:
                    hi = mid
            return lo
    

Funny variation:

    
    
        def guessNumber(self, n):
            lo, hi = 1, n
            while lo < hi:
                mid = (lo + hi) / 2
                lo, hi = ((mid, mid), (mid+1, hi), (lo, mid-1))[guess(mid)]
            return lo
    


### Solution 3
    
    
    int guessNumber(int n) {
            int maxNumber = n, minNumber = 1;
            while (true) {
                int meanNumber = (maxNumber - minNumber) / 2 + minNumber;
                // Do NOT use (maxNumber+minNumber)/2 in case of over flow
                int res = guess(meanNumber);
                if (res == 0) { 
                    return meanNumber;
                } else if (res == 1) {
                    minNumber = meanNumber + 1;
                } else {
                    maxNumber = meanNumber - 1;
                }
            }
        }
    



