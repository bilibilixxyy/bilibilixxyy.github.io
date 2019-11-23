---
layout: post
title: 672. Bulb Switcher II
---
### Question
There is a room with `n` lights which are turned on initially and 4 buttons on
the wall. After performing exactly `m` unknown operations towards buttons, you
need to return how many different kinds of status of the `n` lights could be.

Suppose `n` lights are labeled as number [1, 2, 3 ..., n], function of these 4
buttons are given below:

  1. Flip all the lights.
  2. Flip lights with even numbers.
  3. Flip lights with odd numbers.
  4. Flip lights with (3k + 1) numbers, k = 0, 1, 2, ...



 **Example 1:**

    
    
     **Input:** n = 1, m = 1.
    **Output:** 2
    **Explanation:** Status can be: [on], [off]
    



**Example 2:**

    
    
    **Input:** n = 2, m = 1.
    **Output:** 3
    **Explanation:** Status can be: [on, off], [off, on], [off, off]
    



**Example 3:**

    
    
    **Input:** n = 3, m = 1.
    **Output:** 4
    **Explanation:** Status can be: [off, on, off], [on, off, on], [off, off, off], [off, on, on].
    



 **Note:** `n` and `m` both fit in range [0, 1000].

### Solution 1
We only need to consider special cases which n<=2 and m < 3\. When n >2 and m
>=3, the result is 8.  
The four buttons:

  1. Flip all the lights.
  2. Flip lights with even numbers.
  3. Flip lights with odd numbers.
  4. Flip lights with (3k + 1) numbers, k = 0, 1, 2, ...

If we use button 1 and 2, it equals to use button 3.  
Similarly...

`1 + 2 --> 3, 1 + 3 --> 2, 2 + 3 --> 1`  
So, there are only 8 cases.

`All_on`, `1`, `2`, `3`, `4`, `1+4`, `2+4`, `3+4`

And we can get all the cases, when n>2 and m>=3.

    
    
    class Solution {
        public int flipLights(int n, int m) {
            if(m==0) return 1;
            if(n==1) return 2;
            if(n==2&&m==1) return 3;
            if(n==2) return 4;
            if(m==1) return 4;
            if(m==2) return 7;
            if(m>=3) return 8;
            return 8;
        }
    }
    


### Solution 2
When n <= 1, the solution is trial.

From the 4 types of operations,

    
    
    1. Flip all the lights.
    2. Flip lights with even numbers.
    3. Flip lights with odd numbers.
    4. Flip lights with (3k + 1) numbers, k = 0, 1, 2, ...
    

There are three important observations:

  1. For any operation, only odd or even matters, i.e. 0 or 1. Two same operations equal no operation.
  2. The first 3 operations can be reduced to 1 or 0 operation. For example, flip all + flip even = flip odd. So the result of the first 3 operations is the same as either 1 operation or original.
  3. The solution for n > 3 is the same as n = 3.  
For example, 1 0 0 ....., I use 0 and 1 to represent off and on.  
The state of 2nd digit indicates even flip; The state of 3rd digit indicates
odd flip; And the state difference of 1st and 3rd digits indicates 3k+1 flip.

In summary, the question can be simplified as m <= 3, n <= 3. I am sure you
can figure out the rest easily.

    
    
    class Solution {
    public:
        int flipLights(int n, int m) {
            if (m == 0 || n == 0) return 1;
            if (n == 1) return 2;
            if (n == 2) return m == 1? 3:4;
            if (m == 1) return 4;
            return m == 2? 7:8;
        }
    };
    


### Solution 3
> `1`: light is on  
> `0`: light is off

 **n == 1**

Only **2** possibilities: `1` and `0`.

 **n == 2**

After one operation, it has only **3** possibilities: `00`, `10` and `01`.  
After two and more operations, it has only **4** possibilities: `11`, `10`,
`01` and `00`.

 **n == 3**

After one operation, it has only **4** possibilities: `000`, `101`, `010` and
`011`.  
After two operations, it has **7** possibilities:
`111`,`101`,`010`,`100`,`000`,`001` and `110`.  
After three and more operations, it has **8** possibilities, plus `011` on
above case.

 **n >= 4**

After one operation, it has only **4** possibilities: `0000`, `1010`, `0101`
and `0110`.  
After two or more operations: it has **8** possibilities,
`1111`,`1010`,`0101`,`0111`,`0000`,`0011`, `1100` and `1001`.

    
    
     class Solution {
        public int flipLights(int n, int m) {
            if (m == 0) return 1;
            if (n <= 0 || m < 0) return 0;
            
            if (n == 1) return 2;
            else if (n == 2) return (m == 1) ? 3 : 4;
            else return (m == 1) ? 4 : ((m == 2) ? 7 : 8);
        }
    }
    



