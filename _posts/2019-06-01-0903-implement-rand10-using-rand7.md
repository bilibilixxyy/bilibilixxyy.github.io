---
layout: post
title: 903. Implement Rand10() Using Rand7()
---
### Question
Given a function `rand7` which generates a uniform random integer in the range
1 to 7, write a function `rand10` which generates a uniform random integer in
the range 1 to 10.

Do NOT use system's `Math.random()`.



 **Example 1:**

    
    
     **Input:** 1
    **Output:** [7]
    

**Example 2:**

    
    
    **Input:** 2
    **Output:** [8,4]
    

**Example 3:**

    
    
    **Input:** 3
    **Output:** [8,1,10]
    



 **Note:**

  1. `rand7` is predefined.
  2. Each testcase has one argument: `n`, the number of times that `rand10` is called.



 **Follow up:**

  1. What is the [expected value](https://en.wikipedia.org/wiki/Expected_value) for the number of calls to `rand7()` function?
  2. Could you minimize the number of calls to `rand7()`?

### Solution 1
Idea: `rand7()` -> `rand49()` -> `rand40()` -> `rand10()`

    
    
    public int rand10() {
        int result = 40;
        while (result >= 40) {result = 7 * (rand7() - 1) + (rand7() - 1);}
        return result % 10 + 1;
    }
    

#

### **Time Complexity**

#

The total number of iterations follows **geometric distribution**. For each
iteration in the `while` loop, the probabilty of exiting the loop is _p_ =
40/49. So the average time complexity T(n) = O(1/ _p_ ) = O(49/40) = O(1).

#

### **Correctness**

#

Note that rand49() generates a uniform random integer in [1, 49], so any
number in this range has the same probability to be generated. Suppose _k_ is
an integer in range [1, 40], _P_ (`rand49()` = _k_ ) = 1/49.

    _P_ (result = _k_ )  
= _P_ (`rand49()` = _k_ in the 1st iteration) +  
    _P_ (`rand49()` > 40 in the 1st iteration) * _P_ (`rand49()` = _k_ in the 2nd iteration) +  
    _P_ (`rand49()` > 40 in the 1st iteration) * _P_ (`rand49()` > 40 in the 2nd iteration) * _P_ (`rand49()` = _k_ in the 3rd iteration) +  
    _P_ (`rand49()` > 40 in the 1st iteration) * _P_ (`rand49()` > 40 in the 2nd iteration) * _P_ (`rand49()` > 40 in the 3rd iteration) * _P_ (`rand49()` = _k_ in the 4th iteration) +  
   ...  
= (1/49) + (9/49) * (1/49) + (9/49)^2 * (1/49) + (9/49)^3 * (1/49) + ...  
= (1/49) * [1 + (9/49) + (9/49)^2 + (9/49)^3 + ... ]  
= (1/49) * [1/(1-9/49)]  
= (1/49) * (49/40)  
= 1/40

#

### **Generalization**

#

Implement `randM()` using `randN()` when M > N:  
**Step 1:** Use `randN()` to generate `randX()`, where X >= M. In this
problem, I use `7 * (rand7() - 1) + (rand7() - 1)` to generate `rand49() - 1`.  
**Step 2:** Use `randX()` to generate `randM()`. In this problem, I use
`rand49()` to generate `rand40()` then generate `rand10`.

**Note:** `N^b * (randN() - 1) + N^(b - 1) * (randN() - 1) + N^(b - 2) *
(randN() - 1) + ... + N^0 * (randN() - 1)` generates `randX() - 1`, where `X =
N^(b + 1)`.

#

### **More Examples**

#

(1) Implement `rand11()` using `rand3()`:

    
    
    public int rand11() {
        int result = 22;
        while (result >= 22) {result = 3 * 3 * (rand3() - 1) + 3 * (rand3() - 1) + (rand3() - 1);}
        return result % 11 + 1;
    }
    

Idea: `rand3()` -> `rand27()` -> `rand22` -> `rand11`  
Time Comlexity: O(27/22)

#

(2) Implement `rand9()` using `rand7()`:

    
    
    public int rand9() {
        int result = 45;
        while (result >= 45) {result = 7 * (rand7() - 1) + (rand7() - 1);}
        return result % 9 + 1;
    }
    

Idea: `rand7()` -> `rand49()` -> `rand45()` -> `rand9()`  
Time Comlexity: O(49/45)

#

(3) Implement `rand13()` using `rand6()`:

    
    
    public int rand13() {
        int result = 26;
        while (result >= 26) {result = 6 * (rand6() - 1) + (rand6() - 1);}
        return result % 13 + 1;
    }
    

Idea: `rand6()` -> `rand36()` -> `rand26` -> `rand13()`  
Time Comlexity: O(36/26)


### Solution 2
 **Intuition** :

I saw some solutions generate integer in range 0 ~ 48.  
If `cur < 40`, then `return cur % 10 + 1`.

This a good answer and you may pass a interview with this solution.  
The average call of rand7 here is `2 * 49 / 40 = 2.45`

However, we may think about, what is the limit?  
Is that possible to get an average of 2 calls.

It may seem impossible, but unfortunately, even average 2 is far from the best
answer.

The problem is that you generate 49 random states, waste 9 of them, and
arrange the rest 40 states into 10 states.  
You can see that in that solution, 80% of random states go to waste and you
satisfy with only 20% efficiency.

I did a quick math for the limit `log10 / log7 = 1.183`, which lead me to find
the following solution.  
My solution here got average 1.199, it's really close to theoretic limit.

 **Explanation** :

`rand10()` will consume the generated random integer from stack `cache`.  
If `cache` is empty, it will call function `generate()`.

In `generate()`, it will genrate an integer in range [0, 7^19].  
7^19 = 11398895185373143, and close to an pow of 10.  
So in 11398895185373140 / 11398895185373143 = 99.99999999999997% cases, it
will generate at least 1 integer.  
And in 10000000000000000 / 11398895185373143 = 87.73% cases, it will generate
16 integers.

N = 19 is the best we can choose in long integer range, and N = 7 is another
good choice for 32 bits integer range.  
If we accept big integer, we can be closer to the limit of 1.183 call.

 **C++:**

    
    
        stack< int> cache;
        int rand10() {
            while (cache.size() == 0) generate();
            int res = cache.top(); cache.pop();
            return res;
        }
    
        void generate() {
            int n = 19;
            long cur = 0, range = long(pow(7, n));
            for (int i = 0; i < n; ++i) cur += long(pow(7, i)) * (rand7() - 1);
            while (cur < range / 10 * 10) {
                cache.push(cur % 10 + 1);
                cur /= 10;
                range /= 10;
            }
        }
    

**Java:**

    
    
        Stack<Integer> cache = new Stack<Integer>();
         public int rand10() {
            while (cache.empty()) generate();
            return cache.pop();
        }
    
        void generate() {
            int n = 19;
            long cur = 0, range = (long)Math.pow(7, n);
            for (int i = 0; i < n; ++i) cur += (long)Math.pow(7, i) * (rand7() - 1);
            while (cur < range / 10 * 10) {
                cache.push((int)(cur % 10 + 1));
                cur /= 10;
                range /= 10;
            }
        }
    

**Python:**

    
    
        cache = []
         def rand10(self):
            while not self.cache: self.generate()
            return self.cache.pop()
    
        def generate(self):
            n = 19  # 1.199
            cur = sum((self.rand7() - 1) * (7**i) for i in range(n))
            rang = 7 ** n
            while cur < rang / 10 * 10:
                self.cache.append(cur % 10 + 1)
                cur /= 10
                rang /= 10
    


### Solution 3
    
    
    int rand10() {
        int i,j;
        while( (i = rand7()) > 6);  // P(i is even) = P(i is odd) = 0.5
        while( (j = rand7()) > 5);  // P(j==1) = P(j==2) = P(j==3) = P(j==4) = P(j==5) = 0.2
        return (i&1) ? j : j+5;
    }
    



