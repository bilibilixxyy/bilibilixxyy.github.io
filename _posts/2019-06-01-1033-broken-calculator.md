---
layout: post
title: 1033. Broken Calculator
---
### Question
On a broken calculator that has a number showing on its display, we can
perform two operations:

  * **Double** : Multiply the number on the display by 2, or;
  * **Decrement** : Subtract 1 from the number on the display.

Initially, the calculator is displaying the number `X`.

Return the minimum number of operations needed to display the number `Y`.



 **Example 1:**

    
    
     **Input:** X = 2, Y = 3
    **Output:** 2
    **Explanation:** Use double operation and then decrement operation {2 -> 4 -> 3}.
    

**Example 2:**

    
    
    **Input:** X = 5, Y = 8
    **Output:** 2
    **Explanation:** Use decrement and then double {5 -> 4 -> 8}.
    

**Example 3:**

    
    
    **Input:** X = 3, Y = 10
    **Output:** 3
    **Explanation:** Use double, decrement and double {3 - > 6 -> 5 -> 10}.
    

**Example 4:**

    
    
    **Input:** X = 1024, Y = 1
    **Output:** 1023
    **Explanation:** Use decrement operations 1023 times.
    



 **Note:**

  1. `1 <= X <= 10^9`
  2. `1 <= Y <= 10^9`

### Solution 1
##  **Intuition** :

Considering how to change `Y` to `X`  
Opertation 1: `Y = Y / 2` if `Y` is even  
Opertation 2: `Y = Y + 1`  
  

##  **Explanation** :

Obviously,  
If `Y <= X`, we won't do `Y / 2` anymore.  
We will increase `Y` until it equals to `X`

So before that, while `Y > X`, we'll keep reducing `Y`, until it's smaller
than `X`.  
If `Y` is odd, we can do only `Y = Y + 1`  
If `Y` is even, if we plus 1 to `Y`, then `Y` is odd, we need to plus another
1.  
And because `(Y + 1 + 1) / 2 = (Y / 2) + 1`, 3 operations are more than 2.  
We always choose `Y / 2` if `Y` is even.  
  

##  **Why it's right**

Actually, what we do is:  
If `Y` is even, `Y = Y / 2`  
If `Y` is odd, `Y = (Y + 1) / 2`

We reduce `Y` with least possible operations, until it's smaller than `X`.

And we know that, we won't do `Y + 1` twice in a row.  
Becasue we will always end with an operation `Y / 2`.

2N times `Y + 1` and once `Y / 2` needs 2N + 1 operations.  
Once `Y / 2` first and N times `Y + 1` will end up with same result, but needs
only N + 1 operations.  
  

##  **Time complexity**

We do `Y/2` all the way until it's smaller than `X`,  
time complexity is `O(log(Y/X))`.  
  

 **Java/C++**

    
    
             int res = 0;
            while (Y > X) {
                Y = Y % 2 > 0 ? Y + 1 : Y / 2;
                res++;
            }
            return res + X - Y;
    

**Python:**

    
    
        def brokenCalc(self,  X, Y):
            res = 0
            while X < Y:
                res += Y % 2 + 1
                Y = (Y + 1) / 2
            return res + X - Y
    

  

## **1 line Recursive Solution**

**Java/C++**

    
    
            return  X >= Y ? X - Y : 1 + Y % 2 + brokenCalc(X, (Y + 1) / 2);
    

**Python:**

    
    
             return X - Y if X >= Y else 1 + Y % 2 + self.brokenCalc(X, (Y + 1) / 2)
    

  

## **FAQ, Updated on 2019-02-11**

**Q: Can we try to change X to Y?**  
**A:** Yes we can.

**Java**

    
    
         public int brokenCalc(int X, int Y) {
            int multiple = 1, res = 0;
            while (X * multiple < Y) {
                multiple <<= 1;
                res++;
            }
            int diff = X * multiple - Y;
            while (diff > 0) {  
                res += diff / multiple;
                diff -= diff / multiple * multiple;
                multiple >>= 1;
            }
            return res;
        }
    

**C++**

    
    
         int brokenCalc(int X, int Y) {
            int multiple = 1, res = 0;
            while (X * multiple < Y)
                multiple <<= 1, res++;
            int diff = X * multiple - Y;
            while (diff > 0) {  
                res += diff / multiple;
                diff -= diff / multiple * multiple;
                multiple >>= 1;
            }
            return res;
        }
    

**Python:**

    
    
        def  brokenCalc(self, X, Y):
            multiple = 1
            res = 0
            while X * multiple < Y:
                multiple *= 2
                res += 1
            diff = X * multiple - Y
            while diff:
                res += diff / multiple
                diff -= diff / multiple * multiple
                multiple /= 2
            return res
    


### Solution 2
**Credits** \-- Motivation to convert `y` to `x` inspired by [lee215's
Solution](https://leetcode.com/problems/broken-
calculator/discuss/234484/JavaC%2B%2BPython-Change-Y-to-X-in-1-Line)

First, Let use see if the solution exists or not.  
Clearly, we can keep doubling `x` till it goes beyond `y`. Then we can keep
decrementing `x` till it reaches `y`. Since the number of operations is not
limited, so we conclude that a solution exists.

So now, consider an optimal solution (any solution with the minimal number of
steps).  
The path is nothing but a sequence of numbers that start at `x` and end at
`y`.

`Assume (x<=y)`. The other case is trivial

# Case 1) _**Y is odd**_

Now, consider the last second element of the sequence (optimal path). Recall
that we can only move in the sequence via the allowed moves (in the forward
direction, we multiply by 2 or decrement by 1). Let us backtrack and see which
move did we actually use to get to `y`. (obviously it has to be one of the two
moves).

Now, the move could not have been multiplication by 2, or else `y` would have
been a multiple of 2, which violates our assumption. So the only possible move
is the decrement move. It means that the last second term of the sequence is
indeed `y+1` if `y` is odd. (And there is no other possibility).

So now we just need to compute the optimal length to reach `y+1`, and then add
1 to our answer to get the optimal path length for `y`. (Why? It happens
because `y+1` lies in an optimal path and any subpath of the optimal path must
be optimal or else it would violate our assumptions).

# Case 2) _ **Y is even. Say, y=2m**_

First, let us study the worst case analysis of what is the maximum number that
you would touch if you play optimally.

Clearly it is `2*(y-1)`, since in the worst case, you may end up starting at
`y-1` and jumping to `2*(y-1)` and then coming back. In all other cases, the
jump will lead you to a number less than `2*(y-1)` and you can easily come
back to `y` one step at a time.

Let us denote `2*(y-1)` as `jump_max`.

Now, If `y` is even, we cannot say anything about the last second term of the
sequence. (The move could be either multiplication or decrement).

However, let us see what happens if the last move was multiplication by 2.

Clearly , the last second element in this case is `y/2` = `m`

So we need to compute the optimal path length to reach `m` and then we can add
1 to our answer. (But this is valid only if we know that the last move was
indeed multiplication.)

 _ **What if the last move was decrement?**_

In that case, the last second element becomes `2m+1`, (odd number) , and by
the 1st lemma, we conclude that the last third number is `2m+2`.

Now `2m+2` is an even number so either a jump happens or it's descendant is
`2m+4`. So we keep going to the right untill we find a`k` such that `2m+2k` is
obtained by jumping from `m+k`. Clearly such a number exists as the largest
number we can encounter is `jump_max`.

So, now the path looks like

`x` ....... `(m+k)` -> `2(m+k)` -> `(2m+2k-1)` -> `(2m+2k-2) -> .......`y`

But, if you observe carefully, after we reach `(m+k)` we can decrement `k`
times to reach `m` and then double to get `y`. This would cost us `(k+1)`
operations `+` the cost to reach `(m+k)`. However, the current cost is `(1 +
2(m+k) - 2m)` = `(2k+1)` operations `+` the cost to reach `(m+k)`. Since the
new cost is lower, this violates our assumption that the original sequence was
an optimal path. Therefore we have a contradiction and we conclude that the
last move could not have been decrement.

#  **Conclusion**

If `y` is odd, we know that the last number to be reached before `y` is
`(y+1)` (in the optimal path)

If `y` is even, we know that the last number to be reached before `y` is `y/2`
(in the optimal path).

So finally we have our recursive relation.

`if`(x>=y)  
`cost`(x,y) = x-y

`if`(x< y)  
`cost` (x,y) = 1 + `cost`(x,y+1) if `y` is odd  
`cost` (x,y) = 1 + `cost`(x,y/2) if `y` is even

Here's the iterative implementation

    
    
    int brokenCalc(int x, int y)
    {
        int count=0;
        while(y!=x)
        {
            if(x>=y) return ((x-y) + count);
            
            /* If y is even, the last move was multiplication, else decrement */
            if(y%2==0) y=y/2 ;
            else y++;
            
            // One operation used
            count ++;
        }
        
        return count;
    }
    

Sorry, It got too long but I hope you get the gist.

.


### Solution 3
    
    
    public int brokenCalc(int X, int Y) {
            if (X>=Y) return X-Y;
            return (Y&1)==0? 1+brokenCalc(X, Y/2):1+brokenCalc(X, Y+1);
            
    }
    

So we check only the last bit of Y, since doubling and -1 can only alter
(directly) one bit.

if last bit of Y is 0, the last operation must be doubling, we trace back to
Y/2  
if last bit of Y is 1, the last operation must be decrement, we trace back to
Y+1

Trying to prove that the **if last bit of Y is 0, the last operation must be
doubling** :

 **hypothesis** : there can be one or more decrement from Y+1 to Y in the
shortest path, where last bit of Y is 0

  * since last bit of Y+1 is 1, it must be decrement from Y+2(doubling can never make an 1 on last bit)
  * two options at Y+2.

  1. decrement from Y+3, it's the same as the starting point Y+1 and Y;
  2. doubling from (Y+2)/2, three moves used from (Y+2)/2 to Y: double to Y+2, decrement to Y+1, decrement to Y, while there is a shorter path: decrement to Y/2, double to Y

  * there we get a contradiction to the hypothesis
  * so the hypothesis is false  
hence, **there can be none decrement move(s) from Y+1 to Y in the shortest
path if last bit of Y is 0**



