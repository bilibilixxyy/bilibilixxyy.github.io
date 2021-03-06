---
layout: post
title: 390. Elimination Game
---
### Question
There is a list of sorted integers from 1 to _n_. Starting from left to right,
remove the first number and every other number afterward until you reach the
end of the list.

Repeat the previous step again, but this time from right to left, remove the
right most number and every other number from the remaining numbers.

We keep repeating the steps again, alternating left to right and right to
left, until a single number remains.

Find the last number that remains starting with a list of length _n_.

 **Example:**

    
    
    Input:
    n = 9,
    _1_ 2 _3_ 4 _5_ 6 _7_ 8 _9_
    2 _4_ 6 _8_
    _2_ 6
    6
    
    Output:
    6
    

### Solution 1
    
    
        public int lastRemaining(int n) {
            boolean left = true;
            int remaining = n;
            int step = 1;
            int head = 1;
            while (remaining > 1) {
                if (left || remaining % 2 ==1) {
                    head = head + step;
                }
                remaining = remaining / 2;
                step = step * 2;
                left = !left;
            }
            return head;
        }
    

My idea is to update and record **head** in each turn. when the total number
becomes 1, head is the only number left.

When will head be updated?

  * if we move from left
  * if we move from right and the total remaining number % 2 == 1  
like 2 4 6 8 10, we move from 10, we will take out 10, 6 and 2, head is
deleted and move to 4  
like 2 4 6 8 10 12, we move from 12, we will take out 12, 8, 4, head is still
remaining 2

then we find a rule to update our head.

example:  
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24

  1. Let us start with **head = 1, left = true, step = 1 (times 2 each turn), remaining = n(24)**

  2. we first move from left, we definitely need to move head to next position. **(head = head + step)**  
So after first loop we will have:  
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 - > 2 4 6 8 10
12 14 16 18 20 22 24  
 **head = 2, left = false, step = 1 * 2 = 2, remaining = remaining / 2 = 12**

  3. second loop, we move from right, in what situation we need to move head?  
only if the remaining % 2 == 1, in this case we have 12 % 2 == 0, we don't
touch head.  
so after this second loop we will have:  
2 4 6 8 10 12 14 16 18 20 22 24 - > 2 6 10 14 18 22  
 **head = 2, left = true, step = 2 * 2 = 4, remaining = remaining / 2 = 6**

  4. third loop, we move from left, move head to next position  
after third loop we will have:  
2 6 10 14 18 22 - > 6 14 22  
 **head = 6, left = false, step = 4 * 2 = 8, remaining = remaining / 2 = 3**

  5. fourth loop, we move from right, NOTICE HERE:  
we have remaining(3) % 2 == 1, so we know we need to move head to next
position  
after this loop, we will have  
6 14 22 - > 14  
 **head = 14, left = true, step = 8 * 2 = 16, remaining = remaining / 2 = 1**

  6. while loop end, return head


### Solution 2
After first elimination, all the numbers left are **even** numbers.  
Divide by 2, we get a continuous new sequence from 1 to n / 2.  
For this sequence we start from right to left as the first elimination.  
Then the original result should be two times the mirroring result of
lastRemaining(n / 2).

    
    
    int lastRemaining(int n) {
        return n == 1 ? 1 : 2 * (1 + n / 2 - lastRemaining(n / 2));
    }
    


### Solution 3
    
    
        public int lastRemaining(int n) {
          return leftToRight(n);
        }
    
        private static int leftToRight(int n) {
          if(n <= 2) return n;
          return 2 * rightToLeft(n / 2);
        }
    
        private static int rightToLeft(int n) {
          if(n <= 2) return 1;
          if(n % 2 == 1) return 2 * leftToRight(n / 2);
          return 2 * leftToRight(n / 2) - 1;
        }
    



