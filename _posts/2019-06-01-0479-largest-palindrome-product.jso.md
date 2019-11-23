---
layout: post
title: 479. Largest Palindrome Product
---
### Question
Find the largest palindrome made from the product of two n-digit numbers.

Since the result could be very large, you should return the largest palindrome
mod 1337.



**Example:**

Input: 2

Output: 987

Explanation: 99 x 91 = 9009, 9009 % 1337 = 987



**Note:**

The range of n is [1,8].

### Solution 1
This question just doesn't sound like an easy one to me... ╮(╯﹏╰)╭


### Solution 2
    
    
            public int largestPalindrome(int n) {
            // if input is 1 then max is 9 
            if(n == 1){
                return 9;
            }
            
            // if n = 3 then upperBound = 999 and lowerBound = 99
            int upperBound = (int) Math.pow(10, n) - 1, lowerBound = upperBound / 10;
            long maxNumber = (long) upperBound * (long) upperBound;
            
            // represents the first half of the maximum assumed palindrom.
            // e.g. if n = 3 then maxNumber = 999 x 999 = 998001 so firstHalf = 998
            int firstHalf = (int)(maxNumber / (long) Math.pow(10, n));
            
            boolean palindromFound = false;
            long palindrom = 0;
            
            while (!palindromFound) {
                // creates maximum assumed palindrom
                // e.g. if n = 3 first time the maximum assumed palindrom will be 998 899
                palindrom = createPalindrom(firstHalf);
                
                // here i and palindrom/i forms the two factor of assumed palindrom
                for (long i = upperBound; upperBound > lowerBound; i--) {
                    // if n= 3 none of the factor of palindrom  can be more than 999 or less than square root of assumed palindrom 
                    if (palindrom / i > maxNumber || i * i < palindrom) {
                        break;
                    }
                    
                    // if two factors found, where both of them are n-digits,
                    if (palindrom % i == 0) {
                        palindromFound = true;
                        break;
                    }
                }
    
                firstHalf--;
            }
    
            return (int) (palindrom % 1337);
        }
    
        private long createPalindrom(long num) {
            String str = num + new StringBuilder().append(num).reverse().toString();
            return Long.parseLong(str);
        }
    


### Solution 3
There are two directions for approaching the problem: one is to first
construct the palindrome and then test whether it can be written as the
product of two `n-digit` numbers; the other is to first obtain the product of
the two `n-digit` number and then test whether the product is a palindrome.

* * *

 **`Part I -- From palindrome to product`**

It looks like most of the posts here took the first approach, so I will start
from the first one also. For this approach, we need to consider the following
two problems:

  1. How to construct the palindromes and arrange them in descending order (note we are interested in the maximum palindrome).
  2. How to test if a given palindrome can be written as the product of two `n-digit` numbers.

For the first problem, we need to know how many digits in each of the
palindrome. Since the palindrome is meant to be the product of two `n-digit`
numbers, it can have either `2n` or `2n - 1` digits. And since we are
interested in maximum palindrome, those with `2n` digits will be considered
first. These palindromes can be divided into two parts with equal number of
digits (`n` for each part): `left` and `right`. And `left` will be a mirror
image of `right`, and vice versa. Therefore each palindrome will be fully
determined by either its `left` or `right` part.

Note the `left` part is an `n-digit` number and if we arrange it in descending
order, the resulted palindrome will also be in descending order. Therefore we
may start from the maximum `n-digit` number and go towards the minimum
`n-digit` number. For each number, we can construct the palindrome by
concatenating the number with its mirror image.

Now we come to the second problem, i.e., how to check if the constructed
palindrome can be written as the product of two `n-digit` numbers. This is
essentially the "integer factorization" problem. One straightforward way would
be the "trial division" algorithm, i.e., test each of the `n-digit` number to
see if it is a factor of the palindrome and if so, further check if the other
factor is also an `n-digit` number.

Here is the java problem for this approach. Note we only considered
palindromes with `2n` digits. Fortunately for each case (`n = 1 to 8`), we are
able to find at least one palindrome that can be decomposed into two `n-digit`
numbers, therefore there is no need to check those with `2n - 1` digits
(However if you are still concerned, refer to this
[post](https://discuss.leetcode.com/topic/74533/java-solution-115ms)).

    
    
    public int largestPalindrome(int n) {
        long max = (long)Math.pow(10, n) - 1;
        long min = max / 10;
            
        for (long h = max; h > min; h--) {
            long left = h, right = 0;
            for (long i = h; i != 0; right = right * 10 + i % 10, i /= 10, left *= 10);
            long palindrom = left + right;      // construct the palindrome
                
            for (long i = max; i > min; i--) {
                long j = palindrom / i;
                if (j > i) break;     // terminate if the other number is greater than current number
                if (palindrom % i == 0) return (int)(palindrom % 1337); // found if current number is a factor
            }
        }
    
        return 9;    // account for case n = 1
    }
    

Apparently the algorithm goes exponentially: `O(10^n)`

* * *

**`Part II -- Observations and optimizations`**

Before I continue to the other approach, I'd like to point out some
observations and the corresponding optimizations.

As I mentioned, we are interested in the maximum palindrome, thus it's
reasonable to first check palindromes starting with digit `9`. If not found,
then those starting with digit `8, 7, 6,...`. If the palindrome is starting
with digit `9`, it must also be ending with digit `9`. Now if the palindrome
is the product of the two `n-digit` numbers, the two numbers must be ending
with digits `1, 3, 7, or 9`. Similarly for other cases.

It looks like for each `n`, there exists at least one palindrome with `2n`
digits and starting with digit `9` that can be written as the product of two
`n-digit` numbers. I was able to prove for the case when `n` is even, but
failed for the case when `n` is odd.

If `n` is even, let `n = 2k`. The two `n-digit` numbers can be `num1 = 10^n -
1` and `num2 = 10^n - 10^k + 1`. Their product will be `p = num1 * num2 =
10^(3k) * (10^k - 1) + (10^k - 1) = 9..0..9`, where `p` will have `k` leading
and trailing digit of `9` and `2k` digit `0` in the middle. For `n <= 8`, this
is also the maximum palindrome that is found (not sure if it holds true for
higher `n`, but most likely it will do).

If `n` is odd, the above trick does not work (`p` will have unbalanced `9`'s).
For this case, however, I would like to propose the following statement: let
`n = 2k + 1`, there exists at least two `n-digit` numbers `num1` and `num2`,
`10^n - 10^(k+1) <= num1, num2 <= 10^n - 1`, whose product will be a
palindrome (verified for `n <= 8`).

In summary, we have the following conclusion: for each `n`, there exists at
least two `n-digit` numbers `num1` and `num2`, `10^n - 10^m <= num1, num2 <=
10^n - 1 and m = [(n+1)/2]`, whose product will be a palindrome.

* * *

**`Part III -- From product to palindrome`**

Similar to the first approach, we need to consider the following two problems:

  1. How to construct the products and arrange them in descending order.
  2. How to test if a given product is a palindrome.

The second problem is easy, simply reverse the product and check if it is the
same as the original product. The first problem, however, is a little tricky.
Obtaining the product is a no-brainer. The hard part is how to arrange them in
descending order.

First we need to determine the candidate products. From `part II`, we will
only consider products obtained with the two `n-digit` numbers in the range
`[10^n - 10^m, 10^n - 1]`. Second we will use a PriorityQueue to extract these
candidate products in descending order.

However, the total number of candidates above still blows up the memory when
`n = 8`. So we need further pruning. First again since the product ends with
digit `9`, the two numbers must be ending with digits `1, 3, 7, or 9`. Second,
to avoid repetition, the first number will be no less than the second. Third,
for all products obtained with the first number fixed, there is no need to
include them all at once but instead we can consider them once at a time with
the second number going in decreasing order. So eventually we end up storing
the two numbers in the PriorityQueue while extracting them according to their
product.

With these optimizations, here is the java program for this approach:

    
    
    public int largestPalindrome(int n) {
        int max = (int)Math.pow(10, n) - 1;
        int min = max - (int)Math.pow(10, (n + 1) >> 1);
        	
        Comparator<int[]> cmp = new Comparator<int[]>() {
        	@Override
    	public int compare(int[] a, int[] b) {
        	    return Long.compare((long)b[0] * b[1], (long)a[0] * a[1]);
        	}
        };
        	
        PriorityQueue<int[]> pq = new PriorityQueue<>(max - min, cmp);
        	
        for (int i = max; i > min; i--) {
        	int r = i % 10;
        		
        	if (r == 3 || r == 7) {
        	    pq.offer(new int[] {i, i});
        	} else if (r == 1) {
        	    pq.offer(new int[] {i, i - 2});
        	} else if (r == 9) {
        	    pq.offer(new int[] {i, i - 8});
        	}
        }
        	
        while (!pq.isEmpty()) {
        	int[] a = pq.poll();
        	long p = (long)a[0] * a[1];
        		
        	if (isPalindrome(p)) return (int)(p % 1337);
        		
        	if (a[1] > min) {
        	    a[1] -= 10;
        	    pq.offer(a);
        	}
        }
        
        return 0;
    }
        
    private boolean isPalindrome(long z) {
        long r = 0;
        for (long x = z; x != 0; r = r * 10 + x % 10, x /= 10);
        return r == z;
    }
    

This algorithm still goes exponentially with `n` while also requires extra
space of the same order.



