---
layout: post
title: 728. Self Dividing Numbers
---
### Question
A _self-dividing number_ is a number that is divisible by every digit it
contains.

For example, 128 is a self-dividing number because `128 % 1 == 0`, `128 % 2 ==
0`, and `128 % 8 == 0`.

Also, a self-dividing number is not allowed to contain the digit zero.

Given a lower and upper number bound, output a list of every possible self
dividing number, including the bounds if possible.

 **Example 1:**  

    
    
     **Input:** 
    left = 1, right = 22
    **Output:** [1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 12, 15, 22]
    

**Note:**

* The boundaries of each input argument are `1 <= left <= right <= 10000`.

### Solution 1
## TL;DR

Basic version

    
    
    return [x for x in range(left, right+1) if all([int(i) != 0 and x % int(i)==0 for i in str(x)])]
    

Optimized version:

    
    
    return [x for x in range(left, right+1) if all((i and (x % i==0) for i in map(int, str(x))))]
    

## Step by step:

    
    
    [
        x for x in range(left, right+1)  # iterate all numbers
        if all(  # whether all conditions in the list are true
            [int(i) != 0 and x % int(i)==0  # translate each digit into int and check whether dividable
             for i in str(x)]  # translate int to an iterable string
        )
    ]
    

## Optimization I (default casting from int to boolean)

Use `int(i)` to replace `int(i)==0`.

## Optimization II (short-circuit evaluation with generator)

When evaluating `condition1 and condition2 and condition3`, most advanced
compiler/intepreter will immediately return `False` when `condition1==False`
since evaluating `condition2` and `condition3` make no difference to the
result. This is called [short-circuit
evaluation](https://en.wikipedia.org/wiki/Short-circuit_evaluation).  
In this problem, we can exit the judgement when any one digit fails the test.
In previous code:

    
    
    if all( [... for i in str(x)] )
    

will first evaluate the list and then check whether each one is true. It will
immediately return `False` when it encounter a `False` but it still will first
**evaluate the whole list**.  
To tackle this problem we can change the [_list
comprehension_](https://python-3-patterns-idioms-
test.readthedocs.io/en/latest/Comprehensions.html) to _generator
comprehension_ , which mean we will generate the actualy element in the "list"
when needed. So if the function `all()` detects a `False` in the sequence and
return a `False` immediately, then the following conditions will not be
evaluated.

    
    
    if all( (... for i in str(x)) )
    

## Optimization III (Map a constructor)

Since casting from string to int (`int(i)`) might cost some time, we can cast
the string to int first rather than do the casting twice.  
Some beginners might implement it in this way:

    
    
    map(lambda x: int(x), str_to_convert)
    

But, unlike C++ or other language, the constructor of `int` can be treated as
a mappable function:

    
    
    map(int, str_to_convert)
    

Most tutorials will introduce `map` as `map(function, iterables)`. But we can
also use `map(class, iterables)` directly. Notice that `__init__` is called
even when a static `__call__` is defined.

## Takeaway:

  * List Comprehension in python
  * `all()` function (and `any()` function if you want to do a logic disjunction/or)
  * Short-circuit evaluation
  * Generator (Comprehension)
  * Mapping a class is actually mapping the constructor.


### Solution 2
Pretty self-explanatory.

 _\- Yangshun_

    
    
     class Solution(object):
        def selfDividingNumbers(self, left, right):
            is_self_dividing = lambda num: '0' not in str(num) and all([num % int(digit) == 0 for digit in str(num)])
            return filter(is_self_dividing, range(left, right + 1))
    

As pointed out by @ManuelP, `[num % int(digit) == 0 for digit in str(num)]`
creates an entire list which is not necessary. By leaving out the `[` and `]`,
we can make use of generators which are lazy and allows for short-circuit
evaluation, i.e. `all` will terminate as soon as one of the digits fail the
check.

The answer below improves the run time from 128 ms to 95 ms:

    
    
    class Solution(object):
        def selfDividingNumbers(self, left, right):
            is_self_dividing = lambda num: '0' not in str(num) and all(num % int(digit) == 0 for digit in str(num))
            return filter(is_self_dividing, range(left, right + 1))
    


### Solution 3
The idea is to traverse each integer sequence from left to right incrementing
by one (left, left + 1, left + 2,..., left + n, right). Each time check if the
the current number i is self-divided.  
Self-division check is done by using '%' operator(we check each digit of i
moving from right to left)  
E.g. if i = 128 number:

  1. 128 % 10 = 8, check 8 != 0;
  2. remove 8 from next step j = 128 / 10 = 12
  3. repeat 1 and 2 until j == 0

Time complexity is O(nm), where n = right - left, and m is number of digits in
iterated number

    
    
    class Solution {
        public List<Integer> selfDividingNumbers(int left, int right) {
            List<Integer> list = new ArrayList<>();
            for (int i = left; i <= right; i++) {
                int j = i;
                for (; j > 0; j /= 10) {
                    if ((j % 10 == 0) || (i % (j % 10) != 0)) break;
                }
                if (j == 0) list.add(i); 
            }
            return list;
        }
    }
    



